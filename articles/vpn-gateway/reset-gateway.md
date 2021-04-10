---
title: Återställ VPN-gateway eller anslutning för att återupprätta IPsec-tunnel
titleSuffix: Azure VPN Gateway
description: Återställ en anslutning eller en VPN-gateway för att återupprätta IPsec-tunnlar.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726653"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>Återställa en VPN-gateway eller en anslutning

Att återställa en Azure VPN-gateway eller gateway-anslutning är användbart om du förlorar VPN-anslutningar mellan olika platser på en eller flera plats-till-plats-VPN-tunnlar. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Den här artikeln hjälper dig att återställa en VPN-gateway eller gateway-anslutning.

## <a name="what-happens-during-a-reset"></a>Vad händer under en återställning

### <a name="gateway-reset"></a>Gateway-återställning

En VPN-gateway består av två VM-instanser som körs i en aktiv standby-konfiguration. När du återställer gatewayen startar den om gatewayen och tillämpar sedan de lokala konfigurationerna på den igen. Gatewayen behåller den offentliga IP-adress den redan har. Det innebär att du inte behöver uppdatera VPN-routerkonfigurationen med en ny offentlig IP-adress för Azure VPN-gatewayen.

När du utfärdar kommandot för att återställa gatewayen, startas den aktuella aktiva instansen av Azure VPN-gatewayen om omedelbart. Det kommer att finnas en kort lucka under redundansväxlingen från den aktiva instansen (startas om) till instansen för vänte läge. Pausen bör vara kortare än en minut.

Om anslutningen inte har återställts efter den första omstarten, utfärdar du samma kommando igen för att starta om den andra VM-instansen (den nya aktiva gatewayen). Om de två omstarterna görs direkt efter varandra, uppstår en något längre paus där båda VM-instanserna (aktiv och vänteläge) startas om. Detta leder till en längre lucka i VPN-anslutningen, upp till 45 minuter för virtuella datorer för att slutföra omstarterna.

Efter två omstarter, om du fortfarande har problem med anslutning till olika platser, måste du öppna en supportbegäran från Azure Portal.

### <a name="connection-reset"></a>Anslutningsåterställning

När du väljer att återställa en anslutning startar inte gatewayen om. Endast den valda anslutningen återställs och återställs.

## <a name="reset-a-connection"></a>Återställ en anslutning

Du kan enkelt återställa en anslutning med hjälp av Azure Portal.

1. Navigera till den **anslutning** som du vill återställa. Du kan hitta anslutnings resursen antingen genom att hitta den i **alla resurser**, eller genom att gå till **"Gateway-namnet"-> anslutningar-> "anslutnings namn"**
1. På sidan **anslutning** väljer du **Återställ** på den vänstra menyn.
1. På sidan **Återställ** klickar du på **Återställ** för att återställa anslutningen.

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="Skärm bild som visar återställning.":::

## <a name="reset-a-vpn-gateway"></a>Återställ en VPN gateway

Verifiera nyckelobjekten nedan innan du återställer din gateway för varje IPSec VPN-tunnel (S2S) från plats till plats. Eventuella matchningsfel i objekten resulterar i att S2S VPN-tunnlarna kopplas från. Genom att verifiera och korrigera konfigurationerna för dina lokala och Azure VPN-gatewayer slipper du onödiga omstarter och avbrott för de andra fungerande anslutningarna på gatewayerna.

Verifiera följande objekt innan du återställer din Gateway:

* Internets IP-adresser (VIP) för både Azures VPN-gateway och den lokala VPN-gatewayen har konfigurerats korrekt i både Azures och lokala VPN-principer.
* Den i förväg delade nyckeln måste vara samma på både Azures och de lokala VPN-gatewayerna.
* Om du använder specifik IPsec-/IKE-konfiguration, till exempel kryptering, hash-algoritmer och PFS (Perfect Forward Secrecy), bör du kontrollera att både Azures och lokala VPN-gatewayer har samma konfigurationer.

### <a name="azure-portal"></a><a name="portal"></a>Azure Portal

Du kan återställa en VPN-gateway för Resource Manager med hjälp av Azure Portal. Om du vill återställa en klassisk Gateway, se PowerShell-stegen för den [klassiska distributions modellen](#resetclassic).

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>PowerShell

#### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cmdleten för att återställa en gateway **återställs-AzVirtualNetworkGateway**. Kontrol lera att du har den senaste versionen av [PowerShell AZ-cmdlet: ar](/powershell/module/az.network)innan du utför en återställning. I följande exempel återställs en virtuell nätverksgateway som heter VNet1GW i resurs gruppen TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultat:

När du får ett retur resultat kan du anta att Gateway-återställningen lyckades. Det finns dock inget i det returnerade resultatet som indikerar att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när gatewayen har återställts kan du Visa informationen i [Azure Portal](https://portal.azure.com). I portalen navigerar du till **' GatewayName ' – > Resource Health**.

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klassisk distributionsmodell

Cmdleten för att återställa en gateway **återställs-AzureVNetGateway**. Azure PowerShell-cmdletar för Service Management måste installeras lokalt på Skriv bordet. Du kan inte använda Azure Cloud Shell. Innan du utför en återställning kontrollerar du att du har den senaste versionen av [PowerShell-cmdletarna för Service Management (SM)](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets). När du använder det här kommandot kontrollerar du att du använder det fullständiga namnet på det virtuella nätverket. En klassisk virtuella nätverk som skapats med portalen har ett långt namn som krävs för PowerShell. Du kan visa det långa namnet med hjälp av "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml".

I följande exempel återställs gatewayen för ett virtuellt nätverk med namnet "Group TestRG1 TestVNet1" (som visar som bara "TestVNet1" i portalen):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Resultat:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

### <a name="azure-cli"></a><a name="cli"></a>Azure CLI

För att återställa gatewayen använder du kommandot [AZ Network VNet-Gateway reset](/cli/azure/network/vnet-gateway) . I följande exempel återställs en virtuell nätverksgateway som heter VNet5GW i resurs gruppen TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultat:

När du får ett retur resultat kan du anta att Gateway-återställningen lyckades. Det finns dock inget i det returnerade resultatet som indikerar att återställningen lyckades. Om du vill titta närmare på historiken för att se exakt när gatewayen har återställts kan du Visa informationen i [Azure Portal](https://portal.azure.com). I portalen navigerar du till **' GatewayName ' – > Resource Health**.
