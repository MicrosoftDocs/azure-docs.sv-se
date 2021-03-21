---
title: Självstudie – check lista för nätverks planering
description: Lär dig mer om nätverks kraven för nätverks anslutning och nätverks portar på Azure VMware-lösningen.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 8cee5fa24aab8bd7fe6a9527f9c8e7cdff997511
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462073"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Check lista för nätverks planering för Azure VMware-lösning 

Azure VMware-lösningen erbjuder en privat VMware-miljö som är tillgänglig för användare och program från lokala och Azure-baserade miljöer eller resurser. Anslutningen levereras via nätverks tjänster som Azure ExpressRoute och VPN-anslutningar. Det krävs vissa nätverks adress intervall och brand Väggs portar för att aktivera tjänsterna. Den här artikeln innehåller den information du behöver för att konfigurera nätverket så att det fungerar med Azure VMware-lösningen korrekt.

I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> * Överväganden för virtuella nätverks-och ExpressRoute-kretsar
> * Krav för Routning och undernät
> * Nätverks portar som krävs för att kommunicera med tjänsterna
> * Överväganden för DHCP och DNS i Azure VMware-lösningen

## <a name="prerequisite"></a>Förutsättning
Se till att alla gatewayer, inklusive ExpressRoute-leverantörens tjänst, har stöd för ett autonomt system nummer (ASN) med 4 byte. Azure VMware-lösningen använder 4 bytes offentliga ASN: er för annonserings vägar.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Överväganden för virtuella nätverks-och ExpressRoute-kretsar
När du skapar en virtuell nätverks anslutning i din prenumeration upprättas ExpressRoute-kretsen genom peering, använder en autentiseringsnyckel och ett peering-ID som du begär i Azure Portal. Peering är en privat, en-till-en-anslutning mellan ditt privata moln och det virtuella nätverket.

> [!NOTE] 
> ExpressRoute-kretsen ingår inte i en distribution av privata moln. Den lokala ExpressRoute-kretsen ligger utanför det här dokumentets omfattning. Om du behöver lokal anslutning till ditt privata moln kan du använda en av dina befintliga ExpressRoute-kretsar eller köpa ett i Azure Portal.

När du distribuerar ett privat moln får du IP-adresser för vCenter och NSX-T Manager. Du måste skapa fler resurser i prenumerationens virtuella nätverk för att få åtkomst till dessa hanterings gränssnitt. Du kan hitta procedurerna för att skapa dessa resurser och upprätta [ExpressRoute privata peering](tutorial-expressroute-global-reach-private-cloud.md) i självstudierna.

Det privata molnet logiska nätverk levereras med företablerade NSX-T. En gateway på nivå 0 och nivå 1 har fördefinierats för dig. Du kan skapa ett segment och koppla det till den befintliga nivån-1-gatewayen eller koppla den till en ny nivå 1-gateway som du definierar. NSX-T-T logiska nätverks komponenter ger East-West anslutning mellan arbets belastningar och ger North-South anslutning till Internet och Azure-tjänster.

## <a name="routing-and-subnet-considerations"></a>Överväganden för Routning och undernät
Azure VMware-lösningen privata moln är anslutet till ditt virtuella Azure-nätverk med en Azure ExpressRoute-anslutning. Med den här hög bandbredden kan du få åtkomst till tjänster som körs i din Azure-prenumeration från din privata moln miljö. Routning är Border Gateway Protocol (BGP) baserat, automatiskt etablerade och aktiverat som standard för varje privat moln distribution. 

Privata moln i Azure VMware-lösningar kräver minst ett `/22` CIDR-adressblock för undernät som visas nedan. Det här nätverket kompletterar dina lokala nätverk. Adress blocket får inte överlappa adress block som används i andra virtuella nätverk i din prenumeration och lokala nätverk. I det här adress blocket kan hantering, etablering och vMotion-nätverk tillhandahållas automatiskt.

>[!NOTE]
>Tillåtna intervall för adress blocket är RFC 1918-privata adress utrymmen (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), med undantag för 172.17.0.0/16.

Exempel på `/22` CIDR-nätverks adress block:  `10.10.0.0/22`

Under näten:

| Nätverksanvändning             | Undernät | Exempel          |
| ------------------------- | ------ | ---------------- |
| Hantering av privata moln  | `/26`  | `10.10.0.0/26`   |
| HCX MGMT-migreringar       | `/26`  | `10.10.0.64/26`  |
| Global Reach reserverad     | `/26`  | `10.10.0.128/26` |
| ExpressRoute reserverad     | `/27`  | `10.10.0.192/27` |
| ExpressRoute-peering      | `/27`  | `10.10.0.224/27` |
| Hantering av ESXi           | `/25`  | `10.10.1.0/25`   |
| vMotion-nätverk           | `/25`  | `10.10.1.128/25` |
| Nätverk för replikering       | `/25`  | `10.10.2.0/25`   |
| Virtuellt San                      | `/25`  | `10.10.2.128/25` |
| HCX-överordnad                | `/26`  | `10.10.3.0/26`   |
| Reserverat                  | `/26`  | `10.10.3.64/26`  |
| Reserverat                  | `/26`  | `10.10.3.128/26` |
| Reserverat                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Nätverks portar som krävs

| Källa | Mål | Protokoll | Port | Beskrivning  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| DNS-server för privat moln | Lokal DNS-Server | UDP | 53 | DNS-klient vidarebefordra begär Anden från PC vCenter för alla lokala DNS-frågor (kontrol lera DNS-avsnittet nedan) |  
| Lokal DNS-Server   | DNS-server för privat moln | UDP | 53 | DNS-klient vidarebefordra begär Anden från lokala tjänster till privata moln DNS-servrar (kontrol lera DNS-avsnittet nedan) |  
| Lokalt nätverk  | VCenter-Server för privat moln  | TCP (HTTP)  | 80 | vCenter Server kräver port 80 för direkta HTTP-anslutningar. Port 80 omdirigerar begär anden till HTTPS-port 443. Den här omdirigeringen hjälper om du använder `http://server` i stället för `https://server` .  <br><br>WS-Management (kräver också att port 443 är öppen) <br><br>Om du använder en anpassad Microsoft SQL-databas och inte den paketerade SQL Server 2008-databasen på vCenter Server, används port 80 av SQL Reporting tjänsterna. När du installerar vCenter Server uppmanas du i installations programmet att ändra HTTP-porten för vCenter Server. Ändra vCenter Server HTTP-port till ett anpassat värde för att säkerställa en lyckad installation. Microsoft Internet Information Services (IIS) använder också port 80. Se konflikt mellan vCenter Server och IIS för port 80. |  
| Nätverk för hantering av privata moln | Lokalt Active Directory  | TCP  | 389 | Den här porten måste vara öppen på den lokala och alla fjärrinstanser av vCenter Server. Den här porten är LDAP-portnumret för katalog tjänsterna för vCenter Servers gruppen. VCenter Server systemet måste bindas till port 389, även om du inte ansluter den här vCenter Server-instansen till en grupp med länkade lägen. Om en annan tjänst körs på den här porten kan det vara bättre att ta bort den eller ändra dess port till en annan port. Du kan köra LDAP-tjänsten på vilken port som helst från 1025 till 65535.  Om den här instansen fungerar som Microsoft Windows Active Directory ändrar du Port numret från 389 till en tillgänglig port från 1025 till 65535. Den här porten är valfri för att konfigurera lokal AD som identitets källa i det privata molnet vCenter. |  
| Lokalt nätverk  | VCenter-Server för privat moln  | TCP (HTTPS)  | 443 | Med den här porten kan du komma åt vCenter från ett lokalt nätverk. Standard porten som vCenter Server systemet använder för att lyssna efter anslutningar från vSphere-klienten. Om du vill att vCenter Server systemet ska ta emot data från vSphere-klienten öppnar du port 443 i brand väggen. VCenter Server systemet använder också port 443 för att övervaka data överföring från SDK-klienter. Den här porten används också för följande tjänster: WS-Management (måste också port 80 vara öppen). vSphere-klient åtkomst till vSphere Update Manager. Klient anslutningar från tredje part för nätverks hantering till vCenter Server. Nätverks hanterings klienter från tredje part får åtkomst till värdar. |  
| Webbläsare  | Hybrid moln hanterare  | TCP (HTTPS) | 9443 | Hybrid Cloud Manager Virtual Management Interface för Hybrid Cloud Manager system konfiguration. |
| Admin-nätverk  | Hybrid moln hanterare | SSH | 22 | Administratörs SSH-åtkomst till hybrid moln hanterare. |
| HCM | Cloud Gateway | TCP (HTTPS) | 8123 | Skicka instruktioner för värdbaserade replikeringstjänsten till hybrid molnets Gateway. |
| HCM | Cloud Gateway | HTTP TCP (HTTPS) | 9443 | Skicka hanterings instruktioner till den lokala hybrid moln-gatewayen med hjälp av REST API. |
| Cloud Gateway | L2C | TCP (HTTPS) | 443 | Skicka hanterings instruktioner från Cloud Gateway till L2C när L2C använder samma sökväg som hybrid molnets Gateway. |
| Cloud Gateway | ESXi-värdar | TCP | 80 902 | Hanterings-och OVF-distribution. |
| Molnbaserad Gateway (lokal)| Molnbaserad Gateway (fjärran sluten) | UDP | 4500 | Krävs för IPSEC<br>   IKEv2 (Internet Key Exchange) för att kapsla in arbets belastningar för dubbelriktad tunnel. Nätverks adress Translation-Traversal (NAT-T) stöds också. |
| Molnbaserad Gateway (lokal) | Molnbaserad Gateway (fjärran sluten)  | UDP | 500 | Krävs för IPSEC<br> ISAKMP (Internet Key Exchange) för dubbelriktad tunnel. |
| Lokalt vCenter-nätverk | Nätverk för hantering av privata moln | TCP | 8000 |  vMotion av virtuella datorer från lokala vCenter till privat moln vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Överväganden för DHCP och DNS-matchning
Program och arbets belastningar som körs i en privat moln miljö kräver namn matchning och DHCP-tjänster för sökning och IP-adress tilldelningar. En korrekt DHCP-och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster. Du kan konfigurera en virtuell dator för att tillhandahålla dessa tjänster i din privata moln miljö.  

Använd den DHCP-tjänst som är inbyggd för att NSX eller använda en lokal DHCP-server i det privata molnet i stället för att dirigera broadcast-DHCP-trafik via WAN tillbaka till lokalt.

Mer information finns i artikeln [TILLHANDAHÅLL DHCP-tjänster till NSX-T-nätverk](deploy-azure-vmware-solution.md#optional-provide-dhcp-services-to-nsx-t-network-segment) .


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om överväganden och krav för att distribuera ett privat moln i Azure VMware-lösningen. När du har rätt nätverk på plats kan du fortsätta till nästa självstudie för att skapa ett privat moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Skapa ett privat moln för Azure VMware-lösningen](tutorial-create-private-cloud.md)
