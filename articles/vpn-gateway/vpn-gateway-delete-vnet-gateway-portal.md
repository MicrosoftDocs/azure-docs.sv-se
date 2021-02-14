---
title: 'Azure-VPN Gateway: ta bort en gateway: Portal'
description: Ta bort en virtuell nätverksgateway med hjälp av Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/10/2021
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 413fd8c7f03ef44abe4bece39ca717c533dea66b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376400"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Ta bort en virtuell nätverksgateway med portalen

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Den här artikeln hjälper dig att ta bort en virtuell nätverksgateway. Det finns ett par olika metoder som du kan vidta när du vill ta bort en gateway för en VPN gateway-konfiguration.

* Om du vill ta bort allt och börja om, som i fallet med en test miljö, kan du ta bort resurs gruppen. När du tar bort en resurs grupp tas alla resurser i gruppen bort. Den här metoden rekommenderas endast om du inte vill behålla någon av resurserna i resurs gruppen. Du kan inte selektivt ta bort några få resurser med hjälp av den här metoden.

* Om du vill behålla några av resurserna i resurs gruppen blir det något mer komplicerat att ta bort en virtuell nätverksgateway. Innan du kan ta bort den virtuella Nätverksgatewayen måste du först ta bort alla resurser som är beroende av gatewayen. De steg du följer beror på vilken typ av anslutningar du har skapat och de beroende resurserna för varje anslutning.

> [!IMPORTANT]
> Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Om du vill ta bort en VPN-gateway som har distribuerats med den klassiska distributions modellen följer du stegen i artikeln [ta bort en gateway: klassisk](vpn-gateway-delete-vnet-gateway-classic-powershell.md) .

## <a name="delete-a-vpn-gateway"></a>Ta bort en VPN-gateway

Om du vill ta bort en virtuell nätverksgateway måste du först ta bort alla resurser som hör till den virtuella Nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

I det här läget tas den virtuella Nätverksgatewayen bort.

### <a name="to-delete-additional-resources"></a>Ta bort ytterligare resurser

Följande steg hjälper dig att ta bort alla resurser som inte längre används.

#### <a name="to-delete-the-local-network-gateway"></a>Ta bort den lokala Nätverksgatewayen

1. I **alla resurser**, letar du reda på lokala nätverksgateway som var associerade med varje anslutning.
1. Klicka på **ta bort** på bladet **Översikt** för den lokala Nätverksgatewayen.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Ta bort den offentliga IP-adressresursen för gatewayen

1. I **alla resurser** letar du upp den offentliga IP-adressresursen som var kopplad till gatewayen. Om den virtuella Nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.
1. På sidan **Översikt** för den offentliga IP-adressen klickar du på **ta bort** och sedan på **Ja** för att bekräfta.

#### <a name="to-delete-the-gateway-subnet"></a>Ta bort Gateway-undernätet

1. Leta upp det virtuella nätverket i **alla resurser**. 
1. På bladet **undernät** klickar du på **GatewaySubnet** och sedan på **ta bort**. 
1. Klicka på **Ja** för att bekräfta att du vill ta bort Gateway-undernätet.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Ta bort en VPN-gateway genom att ta bort resurs gruppen

Om du inte är bekymrad över att behålla någon av dina resurser i resurs gruppen och du bara vill börja om kan du ta bort en hel resurs grupp. Det här är ett snabbt sätt att ta bort allt. Följande steg gäller endast för distributions modellen Resource Manager.

1. Leta upp resurs gruppen i **alla resurser** och klicka för att öppna bladet.
1. Klicka på **Ta bort**. På bladet ta bort ser du de resurser som påverkas. Se till att du vill ta bort alla dessa resurser. Annars kan du använda stegen i ta bort en VPN-gateway överst i den här artikeln.
1. Fortsätt genom att skriva namnet på den resurs grupp som du vill ta bort och klicka sedan på **ta bort**.
