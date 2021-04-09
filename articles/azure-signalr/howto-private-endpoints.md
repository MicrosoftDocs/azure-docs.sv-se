---
title: Använda privata slutpunkter
titleSuffix: Azure SignalR Service
description: Översikt över privata slut punkter för säker åtkomst till Azure SignalR-tjänsten från virtuella nätverk.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682638"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Använd privata slut punkter för Azure SignalR service

Du kan använda [privata slut punkter](../private-link/private-endpoint-overview.md) för Azure SignalR-tjänsten för att tillåta klienter i ett virtuellt nätverk (VNet) att på ett säkert sätt få åtkomst till data via en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för Azure SignalR-tjänsten. Nätverks trafik mellan klienterna i VNet-och Azure SignalR-tjänsten passerar över en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Med hjälp av privata slut punkter för Azure SignalR service kan du:

- Skydda Azure SignalR-tjänsten med hjälp av nätverks åtkomst kontrollen för att blockera alla anslutningar på den offentliga slut punkten för Azure SignalR-tjänsten.
- Öka säkerheten för det virtuella nätverket (VNet) genom att göra det möjligt att blockera exfiltrering av data från VNet.
- Anslut säkert till Azure SignalR-tjänster från lokala nätverk som ansluter till VNet med [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

## <a name="conceptual-overview"></a>Begreppsmässig översikt

![Översikt över privata slut punkter för Azure SignalR service](media/howto-private-endpoints/private-endpoint-overview.png)

En privat slut punkt är ett särskilt nätverks gränssnitt för en Azure-tjänst i din [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNet). När du skapar en privat slut punkt för Azure SignalR-tjänsten ger den säker anslutning mellan klienter i ditt VNet och din tjänst. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och Azure SignalR-tjänsten använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till Azure SignalR-tjänsten via den privata slut punkten sömlöst **med samma anslutnings strängar och autentiseringsmetoder som de skulle använda i övrigt**. Privata slut punkter kan användas med alla protokoll som stöds av Azure SignalR-tjänsten, inklusive REST API.

När du skapar en privat slut punkt för en Azure SignalR-tjänst i ditt VNet, skickas en begäran om godkännande till Azure SignalR-tjänstens ägare. Om användaren som begär att den privata slut punkten ska skapas även är ägare till Azure SignalR-tjänsten, godkänns den här medgivande förfrågningen automatiskt.

Azure SignalR service-ägare kan hantera medgivande förfrågningar och privata slut punkter via fliken "*privata slut punkter*" för Azure SignalR-tjänsten i [Azure Portal](https://portal.azure.com).

> [!TIP]
> Om du vill begränsa åtkomsten till Azure SignalR-tjänsten via den privata slut punkten [konfigurerar du nätverks Access Control](howto-network-access-control.md#managing-network-access-control) att neka eller kontrol lera åtkomst via den offentliga slut punkten.

### <a name="connecting-to-private-endpoints"></a>Ansluter till privata slut punkter

Klienter i ett VNet som använder den privata slut punkten bör använda samma anslutnings sträng för Azure SignalR-tjänsten, som klienter som ansluter till den offentliga slut punkten. Vi förlitar sig på DNS-matchning för att automatiskt dirigera anslutningar från VNet till Azure SignalR-tjänsten via en privat länk.

> [!IMPORTANT]
> Använd samma anslutnings sträng för att ansluta till Azure SignalR-tjänsten med privata slut punkter, som du annars skulle använda. Anslut inte till Azure SignalR-tjänsten med dess `privatelink` under domän-URL.

Vi skapar en [privat DNS-zon](../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket med nödvändiga uppdateringar för privata slut punkter som standard. Men om du använder en egen DNS-server kan du behöva göra ytterligare ändringar i DNS-konfigurationen. Avsnittet om [DNS-ändringar](#dns-changes-for-private-endpoints) nedan beskriver de uppdateringar som krävs för privata slut punkter.

## <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter

När du skapar en privat slut punkt uppdateras DNS CNAME-resursposten för Azure SignalR-tjänsten till ett alias i en under domän med prefixet `privatelink` . Som standard skapar vi också en [privat DNS-zon](../dns/private-dns-overview.md)som motsvarar under `privatelink` domänen, med DNS a resurs poster för de privata slut punkterna.

När du matchar ditt Azure SignalR service-domännamn från utanför det virtuella nätverket med den privata slut punkten matchas den offentliga slut punkten för Azure SignalR-tjänsten. När det är löst från det VNet som är värd för den privata slut punkten matchas domän namnet till den privata slut punktens IP-adress.

För det illustrerat exemplet ovan kommer DNS-resursposterna för Azure SignalR-tjänsten "foobar", när de löses från utanför det virtuella nätverket som är värd för den privata slut punkten, att vara:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Som tidigare nämnts kan du neka eller kontrol lera åtkomsten för klienter utanför VNet via den offentliga slut punkten med hjälp av nätverks åtkomst kontrollen.

DNS-resursposterna för ' foobar ', när de löses av en klient i det virtuella nätverk som är värd för den privata slut punkten, blir:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

Den här metoden ger åtkomst till Azure SignalR service **med samma anslutnings sträng** för klienter på det virtuella nätverk som är värd för privata slut punkter, samt klienter utanför VNet.

Om du använder en anpassad DNS-server i ditt nätverk måste klienterna kunna matcha FQDN för Azure SignalR-tjänstens slut punkt till den privata slut punktens IP-adress. Du bör konfigurera DNS-servern så att den delegerar din privata länk under domän till den privata DNS-zonen för det virtuella nätverket eller konfigurera en post för `foobar.privatelink.service.signalr.net` med den privata slut punktens IP-adress.

> [!TIP]
> När du använder en anpassad eller lokal DNS-server bör du konfigurera DNS-servern för att matcha Azure SignalR-tjänstens namn i under `privatelink` domänen med IP-adressen för den privata slut punkten. Du kan göra detta genom att delegera under `privatelink` domänen till det virtuella nätverkets privata DNS-zon, eller konfigurera DNS-zonen på DNS-servern och lägga till DNS-posterna.

Det rekommenderade DNS-zonnamn för privata slut punkter för Azure SignalR service är: `privatelink.service.signalr.net` .

Mer information om hur du konfigurerar en egen DNS-server för att stödja privata slut punkter finns i följande artiklar:

- [Namnmatchning för resurser i virtuella nätverk i Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-konfiguration för privata slut punkter](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Skapa en privat slut punkt tillsammans med en ny Azure SignalR-tjänst i Azure Portal

1. När du skapar en ny Azure SignalR-tjänst väljer du fliken **nätverk** . Välj **privat slut punkt** som anslutnings metod.

    ![Skapa Azure SignalR service – fliken nätverk](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Klicka på **Lägg till**. Fyll i prenumeration, resurs grupp, plats, namn för den nya privata slut punkten. Välj ett virtuellt nätverk och undernät.

    ![Skapa Azure SignalR service – Lägg till privat slut punkt](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Klicka på **Granska + skapa**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Skapa en privat slut punkt för en befintlig Azure SignalR-tjänst i Azure Portal

1. Gå till Azure SignalR-tjänsten.

1. Klicka på menyn Inställningar som kallas **privata slut punkts anslutningar**.

1. Klicka på knappen **+ privat slut punkt** överst.

    ![Bladet anslutningar för privata slut punkter](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Fyll i prenumeration, resurs grupp, resurs namn och region för den nya privata slut punkten.
    
    ![Skapa privat slut punkt – grunder](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Välj mål resurs för Azure SignalR-tjänsten.

    ![Skapa privat slut punkt-resurs](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Välj virtuellt mål nätverk

    ![Skapa privat slut punkt – konfiguration](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Klicka på **Granska + skapa**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Skapa en privat slut punkt med Azure CLI

1. Logga in på Azure CLI
    ```azurecli
    az login
    ```
1. Välj din Azure-prenumeration
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Skapa en ny resursgrupp
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Registrera Microsoft. SignalRService som en provider
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Skapa en ny Azure SignalR-tjänst
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Skapa ett virtuellt nätverk
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Lägga till ett undernät
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Inaktivera Virtual Network principer
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Lägg till en Privat DNS zon
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Länka Privat DNS zon till Virtual Network
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Skapa en privat slut punkt (Godkänn automatiskt)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Skapa en privat slut punkt (begär godkännande manuellt)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Visa anslutnings status
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Priser

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Kända problem

Tänk på följande kända problem med privata slut punkter för Azure SignalR service

### <a name="free-tier"></a>Kostnadsfri nivå

Du kan inte skapa någon privat slut punkt för Azure Signaling-tjänsten på den kostnads fria nivån.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Åtkomst begränsningar för klienter i virtuella nätverk med privata slut punkter

Klienter i virtuella nätverk med befintliga privata slut punkter möter begränsningar vid åtkomst till andra Azure SignalR service-instanser som har privata slut punkter. Anta till exempel att ett VNet N1 har en privat slut punkt för en Azure SignalR service-instans S1. Om Azure SignalR-tjänsten S2 har en privat slut punkt i en VNet-N2 måste klienter i VNet N1 också komma åt Azure SignalR service S2 med en privat slut punkt. Om Azure SignalR-tjänsten S2 inte har några privata slut punkter kan klienter i VNet N1 komma åt Azure SignalR-tjänsten i det kontot utan en privat slut punkt.

Den här begränsningen är ett resultat av DNS-ändringar som gjorts när Azure SignalR service S2 skapar en privat slut punkt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regler för nätverkssäkerhetsgrupper för undernät med privata slutpunkter

För närvarande kan du inte konfigurera regler för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) (NSG) och användardefinierade vägar för privata slut punkter. NSG-regler som tillämpas på det undernät som är värd för den privata slut punkten tillämpas på den privata slut punkten. En begränsad lösning för det här problemet är att implementera åtkomst regler för privata slut punkter på käll under näten, även om den här metoden kan kräva en högre hanterings kostnad.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera nätverks Access Control](howto-network-access-control.md)