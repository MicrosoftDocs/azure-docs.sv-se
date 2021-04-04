---
title: Konfigurera ett VPN för plats-till-plats (S2S) som ska användas med Azure Files | Microsoft Docs
description: Konfigurera en plats-till-plats (S2S) VPN för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0fa3fb8040fd79d68f9260ab520d3b6823ab363d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629299"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurera en plats-till-plats-VPN för användning med Azure Files
Du kan använda en plats-till-plats (S2S) VPN-anslutning för att montera dina Azure-filresurser över SMB från ditt lokala nätverk, utan att öppna port 445. Du kan konfigurera en plats-till-plats-VPN med hjälp av [azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), som är en Azure-resurs som erbjuder VPN-tjänster, och som distribueras i en resurs grupp, tillsammans med lagrings konton eller andra Azure-resurser.

![Ett Topology-diagram som illustrerar topologin för en Azure VPN-gateway som ansluter en Azure-filresurs till en lokal plats med hjälp av en S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Vi rekommenderar starkt att du läser [Azure Files nätverks översikt](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion av de nätverks alternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en plats-till-plats-VPN för att montera Azure-filresurser direkt lokalt. Om du vill dirigera synkron trafik för Azure File Sync över en plats-till-plats-VPN kan du läsa [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Förutsättningar
- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagrings konton, som är hanterings konstruktioner som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. Du kan lära dig mer om hur du distribuerar Azure-filresurser och lagrings konton i [skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slut punkt för det lagrings konto som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slut punkt finns i [konfigurera Azure Files nätverks slut punkter](storage-files-networking-endpoints.md?tabs=azure-portal). 

- En nätverks enhet eller server i ditt lokala data Center som är kompatibelt med Azure VPN Gateway. Azure Files är oberoende av den lokala nätverks enheten som valts, men Azure VPN Gateway underhåller en [lista över testade enheter](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Olika nätverks enheter erbjuder olika funktioner, prestanda egenskaper och hanterings funktioner, så tänk på dessa när du väljer en nätverks enhet.

    Om du inte har en befintlig nätverks installation innehåller Windows Server en inbyggd server roll, Routning och fjärråtkomst (RRAS) som kan användas som lokal nätverks enhet. Mer information om hur du konfigurerar Routning och fjärråtkomst i Windows Server finns i ras- [Gateway](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Lägg till lagrings konto i VNet
I Azure Portal navigerar du till det lagrings konto som innehåller den Azure-filresurs som du vill montera lokalt. I innehålls förteckningen för lagrings kontot väljer du **brand väggar och virtuella nätverk** . Om du inte har lagt till ett virtuellt nätverk till ditt lagrings konto när du skapade det, ska det resulterande fönstret ha alternativet **Tillåt åtkomst från** alternativ för **alla valda nätverk** .

Om du vill lägga till ditt lagrings konto i det önskade virtuella nätverket väljer du **valda nätverk**. Under rubriken **virtuella nätverk** klickar du på antingen **+ Lägg till befintligt virtuellt nätverk** eller **+ Lägg till nytt virtuellt nätverk** beroende på vilket tillstånd som önskas. Att skapa ett nytt virtuellt nätverk leder till att en ny Azure-resurs skapas. Den nya eller befintliga VNet-resursen behöver inte finnas i samma resurs grupp eller prenumeration som lagrings kontot, men det måste finnas i samma region som lagrings kontot och resurs gruppen och prenumerationen som du distribuerar ditt VNet till måste matcha det som du kommer att distribuera din VPN Gateway till. 

![Skärm bild av Azure Portal som ger möjlighet att lägga till ett befintligt eller nytt virtuellt nätverk till lagrings kontot](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Om du lägger till ett befintligt virtuellt nätverk uppmanas du att välja ett eller flera undernät för det virtuella nätverket som lagrings kontot ska läggas till i. Om du väljer ett nytt virtuellt nätverk skapar du ett undernät som en del av skapandet av det virtuella nätverket och du kan lägga till fler senare genom den resulterande Azure-resursen för det virtuella nätverket.

Om du inte har lagt till ett lagrings konto till din prenumeration tidigare måste Microsoft. Storage-tjänstens slut punkt läggas till i det virtuella nätverket. Detta kan ta en stund och tills den här åtgärden har slutförts kan du inte komma åt Azure-filresurserna inom lagrings kontot, inklusive via VPN-anslutningen. 

## <a name="deploy-an-azure-vpn-gateway"></a>Distribuera ett Azure-VPN Gateway
I innehålls förteckningen för Azure Portal väljer du **skapa en ny resurs** och söker efter *virtuell* nätverksgateway. Din virtuella nätverksgateway måste vara i samma prenumeration, Azure-region och resurs grupp som det virtuella nätverk som du distribuerade i föregående steg (Observera att resurs gruppen väljs automatiskt när det virtuella nätverket plockas). 

I syfte att distribuera en Azure-VPN Gateway måste du fylla i följande fält:

- **Namn**: namnet på Azure-resursen för VPN gateway. Namnet kan vara det namn som du tycker är användbart för din hantering.
- **Region**: den region som VPN gateway ska distribueras till.
- **Gateway-typ**: i syfte att distribuera en plats-till-plats-VPN måste du välja **VPN**.
- **VPN-typ**: du kan välja antingen *Route-baserad** eller **Principbaserad** beroende på din VPN-enhet. Routning-baserade VPN-stöd IKEv2, medan principbaserad VPN endast stöder IKEv1. Mer information om de två typerna av VPN-gatewayer finns i [om principbaserad och routning-baserade VPN-gatewayer](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU**: SKU: n styr antalet tillåtna plats-till-plats-tunnlar och önskade prestanda för VPN. Om du vill välja lämplig SKU för ditt användnings fall kan du läsa [Gateway SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) -listan. SKU: n för VPN Gateway kan ändras senare om det behövs.
- **Virtuellt nätverk**: det virtuella nätverk som du skapade i föregående steg.
- **Offentlig IP-adress**: IP-adressen för VPN gateway som ska exponeras för Internet. Förmodligen måste du skapa en ny IP-adress, men du kan också använda en befintlig oanvänd IP-adress om det är lämpligt. Om du väljer att **skapa en ny** IP-adress skapas Azure-resurs i samma resurs grupp som VPN gateway och den  **offentliga IP-adressen** är namnet på den nyligen skapade IP-adressen. Om du väljer **Använd befintlig** måste du välja den befintliga oanvända IP-adressen.
- **Aktivera aktivt-aktivt läge**: Välj **aktive rad** om du skapar en aktiv-aktiv gateway-konfiguration, annars lämna **inaktiverat** markerat. Om du vill veta mer om aktivt-aktivt läge, se [hög tillgänglighet mellan lokala och VNET-till-VNET-anslutning](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Konfigurera BGP ASN**: Välj **aktive ras** endast om konfigurationen kräver den här inställningen. Mer information om den här inställningen finns i [om BGP med Azure VPN gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Välj **Granska + skapa** för att skapa VPN gateway. Det kan ta upp till 45 minuter för en VPN Gateway att skapa och distribuera fullständigt.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Skapa en lokal nätverksgateway för din lokala gateway 
En lokal nätverksgateway är en Azure-resurs som representerar den lokala nätverks enheten. I innehålls förteckningen för Azure Portal väljer du **skapa en ny resurs** och söker efter *lokal* nätverksgateway. Den lokala Nätverksgatewayen är en Azure-resurs som kommer att distribueras tillsammans med ditt lagrings konto, virtuella nätverk och VPN Gateway, men behöver inte finnas i samma resurs grupp eller prenumeration som lagrings kontot. 

I syfte att distribuera den lokala Nätverksgatewayen måste du fylla i följande fält:

- **Namn**: namnet på Azure-resursen för den lokala Nätverksgatewayen. Namnet kan vara det namn som du tycker är användbart för din hantering.
- **IP-adress**: den offentliga IP-adressen för din lokala gateway lokalt.
- **Adress utrymme**: adress intervall för det nätverk som denna lokala nätverksgateway representerar. Du kan lägga till flera adress utrymmes intervall, men se till att de intervall som du anger här inte överlappar med intervall för andra nätverk som du vill ansluta till. 
- **Konfigurera BGP-inställningar**: Konfigurera endast BGP-inställningar om konfigurationen kräver den här inställningen. Mer information om den här inställningen finns i [om BGP med Azure VPN gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Prenumeration**: den önskade prenumerationen. Detta behöver inte matcha den prenumeration som används för VPN Gateway eller lagrings kontot.
- **Resurs grupp**: den önskade resurs gruppen. Detta behöver inte matcha resurs gruppen som används för VPN Gateway eller lagrings kontot.
- **Plats**: Azure-regionen som den lokala nätverks-Gateway-resursen ska skapas i. Detta ska matcha den region som du har valt för VPN Gateway och lagrings kontot.

Välj **skapa** för att skapa den lokala Nätverksgatewayen.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurera lokal nätverks installation
De steg som krävs för att konfigurera den lokala nätverks enheten beror på vilken nätverks enhet som organisationen har valt. Beroende på vilken enhet organisationen har valt kan [listan över testade enheter](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) innehålla en länk till enhets leverantörens instruktioner för att konfigurera med Azure VPN gateway.

## <a name="create-the-site-to-site-connection"></a>Skapa plats-till-plats-anslutningen
För att slutföra distributionen av en S2S VPN måste du skapa en anslutning mellan din lokala nätverks installation (som representeras av den lokala Nätverksgatewayen) och VPN Gateway. Det gör du genom att gå till VPN Gateway som du skapade ovan. I innehålls förteckningen för VPN Gateway väljer du **anslutningar** och klickar på **Lägg till**. Den resulterande rutan **Lägg till anslutning** kräver följande fält:

- **Namn**: namnet på anslutningen. En VPN Gateway kan vara värd för flera anslutningar, så välj ett namn som hjälper dig att hantera som särskiljer den aktuella anslutningen.
- **Anslutnings typ**: sedan en S2S-anslutning väljer du **plats-till-plats (IPSec)** i den nedrullningsbara listan.
- **Virtuell** nätverksgateway: det här fältet väljs automatiskt till den VPN gateway som du gör anslutningen till och kan inte ändras.
- **Lokal** nätverksgateway: det här är den lokala nätverksgateway som du vill ansluta till din VPN gateway. Det resulterande val fönstret ska ha namnet på den lokala Nätverksgatewayen som du skapade ovan.
- **Delad nyckel (PSK)**: en blandning av bokstäver och siffror som används för att upprätta kryptering för anslutningen. Samma delade nyckel måste användas i både det virtuella nätverket och lokala Nätverksgatewayen. Om din gateway-enhet inte tillhandahåller någon kan du göra det här och ge den till din enhet.

Välj **OK** för att skapa anslutningen. Du kan kontrol lera att anslutningen har upprättats via sidan **anslutningar** .

## <a name="mount-azure-file-share"></a>Montera Azure-filresurs 
Det sista steget i att konfigurera en S2S VPN verifierar att det fungerar för Azure Files. Du kan göra detta genom att montera Azure-filresursen lokalt med önskat operativ system. Se anvisningarna för att montera med OS här:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Se även
- [Översikt över Azure Files nätverk](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md)