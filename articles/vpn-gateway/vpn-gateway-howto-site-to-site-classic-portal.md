---
title: 'Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats (klassisk): Portal | Microsoft Docs'
description: Skapa en IPsec-anslutning från ditt lokala nätverk till ett klassiskt virtuellt Azure-nätverk via offentligt Internet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878061"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Skapa en plats-till-plats-anslutning med hjälp av Azure-portalen (klassisk)

Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Stegen i den här artikeln gäller den klassiska distributions modellen och gäller inte för den aktuella distributions modellen, Resource Manager. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

![Diagram över plats-till-plats-anslutning med VPN Gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Bekräfta att du vill arbeta i den klassiska distributionsmodellen. Om du vill arbeta i Resource Manager-distributionsmodellen hittar du information i [Skapa en plats-till-plats-anslutning (Resource Manager)](./tutorial-site-to-site-portal.md). Vi rekommenderar att du använder distributions modellen för Resource Manager, eftersom den klassiska modellen är äldre.
* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.
* PowerShell krävs för att kunna ange den delade nyckeln och skapa VPN gateway-anslutningen. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Exempel på konfigurationsvärden för övningen

Vi använder följande värden i exemplen. Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln. När du arbetar med IP-adress värden för adress utrymme, vill du normalt koordinera med nätverks administratören för att undvika överlappande adress utrymmen, vilket kan påverka routningen. I det här fallet ersätter du IP-adresserna med dina egna om du vill skapa en fungerande anslutning.

* **Resursgrupp:** TestRG1
* **VNet-namn:** TestVNet1
* **Adress utrymme:** 10.11.0.0/16
* **Under näts namn:** Delen
* **Adress intervall för under nätet:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (USA) USA, östra
* **Namn på lokal plats:** Site2
* **Klientadressutrymme:** Adressutrymmet som finns på din lokala plats.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Skapa ett virtuellt nätverk

När du skapar ett virtuellt nätverk som ska användas med en S2S-anslutning måste du se till att adressutrymmena som du anger inte överlappar eventuella klientadressutrymmen för de lokala platser som du vill ansluta till. Om du har överlappande undernät fungerar inte anslutningen ordentligt.

* Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk.

* Om du inte redan har ett virtuellt nätverk, skapa ett. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna.

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Konfigurera platsen och gatewayen

### <a name="to-configure-the-site"></a>Konfigurera platsen

Den lokala platsen avser vanligtvis din lokala plats. Den innehåller IP-adressen för den VPN-enhet som du skapar en anslutning till och IP-adressintervallen som ska dirigeras via VPN-gatewayen till VPN-enheten.

1. Välj **plats-till-plats-anslutningar** under **Inställningar** på sidan för ditt VNet.
1. På sidan plats-till-plats-anslutningar väljer du **+ Lägg till**.
1. På sidan **Konfigurera en VPN-anslutning och gateway** för **Anslutnings typ** lämnar **du plats-till-plats-** vald. I den här övningen kommer du att behöva använda en kombination av [exempel värden](#values) och dina egna värden.

   * **IP-adress till VPN-gateway:** Det här är den offentliga IP-adressen till VPN-enheten för ditt lokala nätverk. VPN-enheten måste ha en offentlig IP-adress (IPv4). Ange en giltig offentlig IP-adress för VPN-enheten som du vill ansluta till. Det måste kunna kontaktas av Azure. Om du inte vet VPN-enhetens IP-adress kan du använda ett platshållarvärde (i formatet för en giltig offentlig IP-adress) och ändra det senare.

   * **Klientadressutrymme:** Visar IP-adressintervall som du vill dirigera till det lokala nätverket via denna gateway. Du kan lägga till flera adressintervall. Se till att intervallen du anger här inte överlappar intervallen för andra nätverk som ditt virtuella nätverk ansluter till, eller överlappar adressintervallen för det virtuella nätverket.
1. Klicka inte på granska + skapa längst ned på sidan. I stället väljer du **Nästa: Gateway>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Så här konfigurerar du den virtuella Nätverksgatewayen

1. På sidan **Gateway** väljer du följande värden:

   * **Storlek:** Det här är Gateway-SKU: n som du använder för att skapa din virtuella nätverksgateway. Klassiska VPN-gatewayer använder de gamla (äldre) gateway-SKU:erna. Mer information om de äldre gateway-SKU:erna finns i [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Arbeta med SKU:er för virtuella nätverksgatewayer (gamla SKU:er)). Du kan välja **standard** för den här övningen.

   * **Typ av dirigering:** Välj typ av routning för din gateway. Detta kallas även för VPN-typ. Det är viktigt att välja rätt typ, eftersom du inte kan konvertera gatewayen från en typ till en annan. VPN-enheten måste vara kompatibel med den routningstyp som du väljer. Mer information om Dirigerings typ finns i [om VPN Gateway inställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Läs artiklarna om VPN-typerna RouteBased och PolicyBased. ”Dynamiska” motsvarar RouteBased och ”statiska” motsvarar PolicyBased. Normalt vill du ha dynamisk routning.

   * **Gateway-undernät:** Storleken på det Gateway-undernät som du anger beror på konfigurationen av VPN-gatewayen som du vill skapa. Även om det går att skapa ett gatewayundernät som är så litet som /29 rekommenderar vi att du använder /27 eller /28. Då skapas ett större undernät som innehåller fler adresser. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

1. Välj **Granska + skapa** längst ned på sidan för att kontrol lera inställningarna. Välj **skapa** för att distribuera. Det kan ta upp till 45 minuter att skapa en virtuell nätverksgateway, beroende på vilken Gateway-SKU som du har valt.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande värden:

* En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
* Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Hämta värden

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Skapa anslutningen

> [!NOTE]
> För den klassiska distributions modellen är det här steget inte tillgängligt i Azure Portal eller via Azure Cloud Shell. Du måste använda SM-versionen (Service Management) av Azure PowerShell-cmdlets lokalt från Skriv bordet.
>

I det här steget, med hjälp av värdena från föregående steg, ställer du in den delade nyckeln och skapar anslutningen. Nyckeln som du anger måste vara samma nyckel som används i konfigurationen för VPN-enheten.

1. Ange den delade nyckeln och skapa anslutningen.

   * Ändra värdet VNetName och värdet-LocalNetworkSiteName. När du anger ett namn som innehåller blanksteg ska du ange värdet inom enkla citattecken.
   * -SharedKey är ett värde som du genererar och sedan anger. I exemplet använde vi "vi abc123", men du kan (och bör) generera något mer komplicerat. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. När anslutningen har skapats visas resultatet: **Status: Lyckades**.

## <a name="verify-your-connection"></a><a name="verify"></a>Verifiera din anslutning

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Om du har problem med att ansluta läser du **felsökningsavsnittet** som du hittar i innehållsförteckningen till vänster.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Återställa en VPN-gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Stegvisa anvisningar finns i [Återställa en VPN-gateway](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Ändra en gateway-SKU

Anvisningar för hur du ändrar en gateway-SKU finns i [ändra storlek på en gateway-SKU](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](../index.yml).
* Information om Tvingad tunnel trafik finns i [om Tvingad tunnel trafik](vpn-gateway-about-forced-tunneling.md).