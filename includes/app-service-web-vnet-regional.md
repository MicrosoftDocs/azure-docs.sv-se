---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609383"
---
Genom att använda regional VNet-integrering kan din app komma åt:

* Resurser i ett VNet i samma region som din app.
* Resurser i virtuella nätverk som peer-kopplas till det virtuella nätverk som din app är integrerat med.
* Säkra tjänster för tjänst slut punkt.
* Resurser över Azure ExpressRoute-anslutningar.
* Resurser i det virtuella nätverk som du är integrerad med.
* Resurser mellan peer-anslutningar, inklusive Azure ExpressRoute-anslutningar.
* Privata slutpunkter 

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande funktioner i Azure-nätverk:

* **Nätverks säkerhets grupper (NSG: er)**: du kan blockera utgående trafik med en NSG som placeras i ditt integrations undernät. Reglerna för inkommande trafik gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din app.
* **Routningstabeller (UDR)**: du kan placera en routningstabell i integrations under nätet för att skicka utgående trafik där du vill.

Som standard dirigerar din app endast RFC1918 trafik till ditt VNet. Om du vill dirigera all utgående trafik till ditt VNet använder du följande steg för att lägga till `WEBSITE_VNET_ROUTE_ALL` inställningen i appen: 

1. Gå till **konfigurations** gränssnittet på din app-Portal. Välj **Ny programinställning**.
1. Ange `WEBSITE_VNET_ROUTE_ALL` i rutan **namn** och ange `1` i rutan **värde** .

   ![Ange program inställning][4]

1. Välj **OK**.
1. Välj **Spara**.

> [!NOTE]
> När du dirigerar all utgående trafik till ditt VNet, omfattas den av NSG: er och UDR som tillämpas på ditt integrations undernät. När `WEBSITE_VNET_ROUTE_ALL` är inställt på `1` skickas utgående trafik fortfarande från de adresser som anges i dina app-egenskaper, om du inte anger vägar som dirigerar trafiken någon annan stans.
> 
> Regional VNet-integrering kan inte använda port 25.

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte komma åt resurser över global peering anslutningar.
* Funktionen är tillgänglig från alla App Service skalnings enheter i Premium v2 och Premium v3. Den är också tillgänglig i standard men endast från nyare App Service skalnings enheter. Om du använder en äldre skalnings enhet kan du bara använda funktionen från en Premium v2-App Service plan. Om du vill vara säker på att du kan använda funktionen i en standard App Service plan skapar du din app i en Premium v3-App Service plan. Dessa planer stöds endast på våra nyaste skalnings enheter. Du kan skala ned om du vill.  
* Integrations under nätet kan bara användas av en App Service plan.
* Funktionen kan inte användas av isolerade plan-appar som finns i en App Service-miljön.
* Funktionen kräver ett oanvänt undernät som är a/28 eller större i ett Azure Resource Manager VNet.
* Appen och VNet måste finnas i samma region.
* Du kan inte ta bort ett VNet med en integrerad app. Ta bort integrationen innan du tar bort det virtuella nätverket.
* Du kan bara ha en regional VNet-integration per App Service plan. Flera appar i samma App Service plan kan använda samma VNet.
* Du kan inte ändra prenumerationen på en app eller en plan när det finns en app som använder regional VNet-integrering.
* Din app kan inte matcha adresser i Azure DNS Private Zones utan konfigurations ändringar.

VNet-integreringen är beroende av ett dedikerat undernät. När du etablerar ett undernät förlorar Azure-undernätet fem IP-adresser från start. En adress används från integrations under nätet för varje plan instans. När du skalar din app till fyra instanser används fyra adresser. 

När du skalar upp eller ned i storlek dubbleras det nödvändiga adress utrymmet under en kort tids period. Detta påverkar de verkliga, tillgängliga instanser som stöds för en specifik under näts storlek. I följande tabell visas både de maximalt tillgängliga adresserna per CIDR-block och effekten detta har i horisontell skala:

| CIDR-block storlek | Maximalt antal tillgängliga adresser | Max horisontell skala (instanser)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Förutsätter att du behöver skala upp eller ned i valfri storlek eller SKU. 

Eftersom under näts storleken inte kan ändras efter tilldelningen använder du ett undernät som är tillräckligt stort för att anpassa vilken skala appen kan komma åt. För att undvika eventuella problem med under näts kapaciteten bör du använda en/26 med 64-adresser.  

När du vill att dina appar i en annan plan ska komma åt ett VNet som redan är anslutet till av appar i ett annat abonnemang väljer du ett annat undernät än det som används av den befintliga VNet-integreringen.

Funktionen stöds fullt ut för både Windows-och Linux-appar, inklusive [anpassade behållare](../articles/app-service/quickstart-custom-container.md). Alla beteenden fungerar på samma sätt mellan Windows-appar och Linux-appar.

### <a name="service-endpoints"></a>Tjänstslutpunkter

Regional VNet-integrering gör att du kan komma åt Azure-tjänster som skyddas med tjänst slut punkter. För att få åtkomst till en tjänst slut punkt – säker tjänst måste du göra följande:

1. Konfigurera regional VNet-integrering med din webbapp för att ansluta till ett speciellt undernät för integrering.
1. Gå till mål tjänsten och konfigurera tjänstens slut punkter mot integrations under nätet.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Du kan använda nätverks säkerhets grupper för att blockera inkommande och utgående trafik till resurser i ett VNet. En app som använder regional VNet-integrering kan använda en [nätverks säkerhets grupp][VNETnsg] för att blockera utgående trafik till resurser i ditt VNet eller Internet. Om du vill blockera trafik till offentliga adresser måste du ange program inställningen `WEBSITE_VNET_ROUTE_ALL` till `1` . Reglerna för inkommande trafik i en NSG gäller inte för din app eftersom VNet-integrering endast påverkar utgående trafik från din app.

Om du vill kontrol lera inkommande trafik till din app använder du funktionen begränsningar för åtkomst. En NSG som tillämpas på ditt integrations under nät gäller oavsett vilka vägar som tillämpas på ditt integrations under nät. Om `WEBSITE_VNET_ROUTE_ALL` är inställt på `1` och du inte har några vägar som påverkar offentlig adress trafik i ditt integrations undernät, omfattas all utgående trafik fortfarande till NSG: er som har tilldelats ditt integrations under nät. När `WEBSITE_VNET_ROUTE_ALL` inte är inställt tillämpas NSG: er endast på RFC1918-trafik.

### <a name="routes"></a>Vägar

Du kan använda routningstabeller för att dirigera utgående trafik från din app till var du vill. Som standard påverkar routningstabeller bara din RFC1918-destinations trafik. När du väljer `WEBSITE_VNET_ROUTE_ALL` till `1` påverkas alla utgående samtal. Vägar som anges i ditt integrations undernät påverkar inte svar på inkommande app-begäranden. Vanliga mål kan omfatta brand Väggs enheter eller gatewayer.

Om du vill dirigera all utgående trafik lokalt kan du använda en routningstabell för att skicka all utgående trafik till din ExpressRoute-Gateway. Om du dirigerar trafik till en gateway måste du ställa in vägar i det externa nätverket för att skicka svar tillbaka.

Border Gateway Protocol (BGP) vägar påverkar också din app-trafik. Om du har BGP-vägar från något som liknar en ExpressRoute-Gateway påverkas din utgående trafik för appen. BGP-vägar påverkar som standard bara din RFC1918-destinations trafik. När `WEBSITE_VNET_ROUTE_ALL` är inställt på `1` kan all utgående trafik påverkas av BGP-vägarna.

### <a name="azure-dns-private-zones"></a>Azure DNS privata zoner 

När din app har integrerats med ditt VNet, använder den samma DNS-server som ditt VNet har kon figurer ATS med. Som standard fungerar inte appen med Azure DNS privata zoner. Om du vill arbeta med Azure DNS privata zoner måste du lägga till följande appinställningar:

1. `WEBSITE_DNS_SERVER` med värde `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` med värde `1`

De här inställningarna skickar alla utgående samtal från din app till ditt VNet och gör att din app får åtkomst till en Azure DNS privat zon. Med de här inställningarna kan din app använda Azure DNS genom att fråga den privata DNS-zonen på arbets nivå.  

### <a name="private-endpoints"></a>Privata slutpunkter

Om du vill ringa till [privata slut punkter][privateendpoints]måste du kontrol lera att dina DNS-sökningar matchar den privata slut punkten. Du kan använda det här beteendet på något av följande sätt: 

* Integrera med Azure DNS privata zoner. Detta görs automatiskt när ditt VNet inte har en anpassad DNS-server.
* Hantera den privata slut punkten på den DNS-server som används av din app. Om du vill göra det måste du känna till adressen till den privata slut punkten och sedan peka slut punkten som du försöker ansluta till adressen med en A-post.
* Konfigurera din egen DNS-server för att vidarebefordra till Azure DNS privata zoner.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
