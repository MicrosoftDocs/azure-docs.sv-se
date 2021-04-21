---
title: Konfigurera ett VPN för plats-till-plats (S2S) för användning med Azure Files | Microsoft Docs
description: Så här konfigurerar du ett VPN för plats-till-plats (S2S) för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a08ca4142876a5a92adbe8b1c3fce9ec7953019
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778021"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurera ett plats-till-plats-VPN för användning med Azure Files
Du kan använda en VPN-anslutning från plats till plats (S2S) för att montera dina Azure-filresurser via SMB från ditt lokala nätverk, utan att öppna port 445. Du kan konfigurera en VPN för plats till plats med Hjälp av [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), som är en Azure-resurs som erbjuder VPN-tjänster och som distribueras i en resursgrupp tillsammans med lagringskonton eller andra Azure-resurser.

![Ett topologidiagram som illustrerar topologin för en Azure VPN-gateway som ansluter en Azure-filresurs till en lokal plats med S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Vi rekommenderar starkt att du [läser Azure Files](storage-files-networking-overview.md) översikt över nätverk innan du fortsätter med den här artikeln för en fullständig beskrivning av de nätverksalternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en plats-till-plats-VPN för att montera Azure-filresurser direkt lokalt. Om du vill dirigera synkroniseringstrafik för Azure File Sync via en plats-till-plats-VPN kan du gå till konfigurera Azure File Sync proxy och [brandväggsinställningar.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Förutsättningar
- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagringskonton, som är hanteringskonstruktioner som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blobcontainrar eller köer. Du kan lära dig mer om hur du distribuerar Azure-filresurser och lagringskonton [i Skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slutpunkt för lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slutpunkt finns i [Konfigurera Azure Files nätverksslutpunkter](storage-files-networking-endpoints.md?tabs=azure-portal). 

- En nätverksinstallation eller server i ditt lokala datacenter som är kompatibel med Azure VPN Gateway. Azure Files är agnostisk för den lokala nätverksinstallationen som valts, men Azure VPN Gateway har en lista [över testade enheter](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Olika nätverksutrustning erbjuder olika funktioner, prestandaegenskaper och hanteringsfunktioner, så tänk på dem när du väljer en nätverksutrustning.

    Om du inte har en befintlig nätverkinstallation innehåller Windows Server en inbyggd serverroll, routning och fjärråtkomst (RRAS), som kan användas som den lokala nätverksinstallationen. Mer information om hur du konfigurerar routning och fjärråtkomst i Windows Server finns [i RAS Gateway](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Lägga till lagringskonto i VNet
I Azure Portal navigerar du till lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. I innehållsförteckningen för lagringskontot väljer du posten **Brandväggar och virtuella** nätverk. Såvida du inte har lagt till ett virtuellt nätverk till ditt **lagringskonto** när du skapade det, bör alternativknappen Tillåt **åtkomst från** för Alla nätverk vara markerad i fönstret som visas.

Om du vill lägga till ditt lagringskonto i det virtuella nätverket väljer du **Valda nätverk.** Under det **virtuella nätverket** klickar du antingen på + Lägg till **befintligt virtuellt nätverk** eller **+Lägg till nytt virtuellt** nätverk beroende på önskat tillstånd. Om du skapar ett nytt virtuellt nätverk skapas en ny Azure-resurs. Den nya eller befintliga VNet-resursen behöver inte finnas i samma resursgrupp eller prenumeration som lagringskontot, men den måste finnas i samma region som lagringskontot och resursgruppen och prenumerationen som du distribuerar ditt VNet till måste matcha den som du ska distribuera din VPN Gateway till. 

![Skärmbild av Azure Portal ger möjlighet att lägga till ett befintligt eller nytt virtuellt nätverk till lagringskontot](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Om du lägger till ett befintligt virtuellt nätverk uppmanas du att välja ett eller flera undernät i det virtuella nätverket som lagringskontot ska läggas till i. Om du väljer ett nytt virtuellt nätverk skapar du ett undernät som en del av skapandet av det virtuella nätverket och du kan lägga till fler senare via den resulterande Azure-resursen för det virtuella nätverket.

Om du inte har lagt till ett lagringskonto i din prenumeration tidigare måste tjänstslutpunkten Microsoft.Storage läggas till i det virtuella nätverket. Det kan ta lite tid, och innan den här åtgärden har slutförts kan du inte komma åt Azure-filresurser i det lagringskontot, inklusive via VPN-anslutningen. 

## <a name="deploy-an-azure-vpn-gateway"></a>Distribuera en Azure-VPN Gateway
I innehållsförteckningen för den virtuella Azure Portal väljer du **Skapa en ny resurs och** söker efter Virtuell *nätverksgateway*. Din virtuella nätverksgateway måste finnas i samma prenumeration, Azure-region och resursgrupp som det virtuella nätverket som du distribuerade i föregående steg (observera att resursgruppen väljs automatiskt när det virtuella nätverket väljs). 

För att distribuera en Azure-VPN Gateway måste du fylla i följande fält:

- **Namn:** Namnet på Azure-resursen för VPN Gateway. Det här namnet kan vara ett namn som du tycker är användbart för din hantering.
- **Region:** Den region som VPN Gateway distribueras till.
- **Gatewaytyp:** För att distribuera en plats-till-plats-VPN måste du välja **VPN.**
- **VPN-typ:** Du kan välja *antingen Ruttbaserad** eller **Principbaserad** beroende på din VPN-enhet. Ruttbaserade VPN:er stöder IKEv2, medan principbaserade VPN:er endast stöder IKEv1. Mer information om de två typerna av VPN-gatewayer finns [i Om principbaserade och ruttbaserade VPN-gatewayer](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU:** SKU:n styr antalet tillåtna plats-till-plats-tunnlar och önskad prestanda för VPN. Om du vill välja lämplig SKU för ditt användningsfall kan du läsa [listan med gateway-SKU:er.](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) SKU:n för VPN Gateway kan ändras senare om det behövs.
- **Virtuellt** nätverk: Det virtuella nätverk som du skapade i föregående steg.
- **Offentlig IP-adress:** IP-VPN Gateway som ska exponeras mot Internet. Du måste förmodligen skapa en ny IP-adress, men du kan också använda en befintlig oanvänd IP-adress om det är lämpligt. Om du väljer Skapa ny skapas en ny IP-adress för Azure-resursen i samma resursgrupp som VPN Gateway och namnet på den offentliga **IP-adressen** blir namnet på den nyligen skapade IP-adressen. Om du väljer **Använd befintlig** måste du välja den befintliga oanvända IP-adressen.
- **Aktivera läget aktiv-aktiv:** Välj endast **Aktiverad om** du skapar en gatewaykonfiguration för aktiv-aktiv, annars lämnar du **Inaktiverad** markerad. Mer information om läget aktiv-aktiv finns i Hög tillgänglig anslutning mellan [platser och VNet-till-VNet-anslutning.](../../vpn-gateway/vpn-gateway-highlyavailable.md)
- **Konfigurera BGP ASN:** Välj endast **Aktiverad om** konfigurationen kräver den här inställningen. Mer information om den här inställningen finns i [Om BGP med Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Välj **Granska + skapa** för att skapa VPN Gateway. En VPN Gateway kan ta upp till 45 minuter att skapa och distribuera fullständigt.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Skapa en lokal nätverksgateway för din lokala gateway 
En lokal nätverksgateway är en Azure-resurs som representerar din lokala nätverksinstallation. I innehållsförteckningen för den Azure Portal väljer du **Skapa en ny resurs och söker** efter den lokala *nätverksgatewayen*. Den lokala nätverksgatewayen är en Azure-resurs som distribueras tillsammans med ditt lagringskonto, virtuella nätverk och VPN Gateway, men som inte behöver finnas i samma resursgrupp eller prenumeration som lagringskontot. 

För att distribuera den lokala nätverksgatewayresursen måste du fylla i följande fält:

- **Namn:** Namnet på Azure-resursen för den lokala nätverksgatewayen. Det här namnet kan vara ett namn som du tycker är användbart för din hantering.
- **IP-adress:** Den offentliga IP-adressen för din lokala gateway lokalt.
- **Adressutrymme:** Adressintervallen för nätverket som den lokala nätverksgatewayen representerar. Du kan lägga till flera adressutrymmesintervall, men se till att intervallen som du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. 
- **Konfigurera BGP-inställningar:** Konfigurera endast BGP-inställningar om konfigurationen kräver den här inställningen. Mer information om den här inställningen finns i [Om BGP med Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Prenumeration:** Den önskade prenumerationen. Detta behöver inte matcha prenumerationen som används för VPN Gateway eller lagringskontot.
- **Resursgrupp:** Önskad resursgrupp. Detta behöver inte matcha resursgruppen som används för VPN Gateway eller lagringskontot.
- **Plats:** Azure-regionen som den lokala nätverksgatewayresursen ska skapas i. Detta bör matcha den region som du valde för VPN Gateway och lagringskontot.

Välj **Skapa för** att skapa den lokala nätverksgatewayresursen.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurera en lokal nätverksinstallation
De specifika stegen för att konfigurera den lokala nätverksinstallationen beror på vilken nätverksinstallation din organisation har valt. Beroende på vilken enhet din [](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) organisation har valt kan listan över testade enheter ha en länk till enhetsleverantörens instruktioner för att konfigurera med Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Skapa plats-till-plats-anslutningen
För att slutföra distributionen av ett S2S VPN måste du skapa en anslutning mellan din lokala nätverksinstallation (som representeras av den lokala nätverksgatewayresursen) och VPN Gateway. Det gör du genom att gå till VPN Gateway som du skapade ovan. I innehållsförteckningen för den VPN Gateway väljer du Anslutningar och **klickar** på Lägg **till.** Det resulterande **fönstret Lägg till** anslutning kräver följande fält:

- **Namn:** Namnet på anslutningen. En VPN Gateway kan vara värd för flera anslutningar, så välj ett namn som är användbart för din hantering som särskiljer den här specifika anslutningen.
- **Anslutningstyp:** Eftersom det här är en S2S-anslutning väljer du **Plats-till-plats (IPSec)** i listrutan.
- **Virtuell nätverksgateway:** Det här fältet markeras automatiskt VPN Gateway som du upprättar anslutningen till och kan inte ändras.
- **Lokal nätverksgateway:** Det här är den lokala nätverksgateway som du vill ansluta till VPN Gateway. Det resulterande urvalsfönstret ska ha namnet på den lokala nätverksgateway som du skapade ovan.
- **Delad nyckel (PSK):** En blandning av bokstäver och siffror som används för att upprätta kryptering för anslutningen. Samma delade nyckel måste användas i både det virtuella nätverket och lokala nätverksgatewayer. Om din gateway-enhet inte tillhandahåller någon kan du hitta på en här och ange den för din enhet.

Välj **OK** för att skapa anslutningen. Du kan kontrollera att anslutningen har gjorts via **sidan** Anslutningar.

## <a name="mount-azure-file-share"></a>Montera En Azure-filresurs 
Det sista steget i att konfigurera ett S2S VPN är att verifiera att det fungerar för Azure Files. Du kan göra detta genom att montera din Azure-filresurs lokalt med önskat operativsystem. Se anvisningarna för att montera med operativsystem här:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Se även
- [Azure Files nätverksöversikt](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats-VPN (P2S) i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera en punkt-till-plats-VPN (P2S) i Linux för användning med Azure Files](storage-files-configure-p2s-vpn-linux.md)