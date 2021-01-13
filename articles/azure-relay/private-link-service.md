---
title: Integrera Azure Relay med Azure Private Link service
description: Lär dig hur du integrerar Azure Relay med Azure Private Link service
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134389"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Integrera Azure Relay med en privat Azure-länk 
Azure **Private Link service** ger dig åtkomst till Azure-tjänster (till exempel Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage och Azure Cosmos dB) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket. Mer information finns i [Vad är en privat Azure-länk?](../private-link/private-link-overview.md)

En **privat slut punkt** är ett nätverks gränssnitt som gör att dina arbets belastningar som körs i ett virtuellt nätverk kan ansluta privat och säkert till en tjänst som har en **privat länk resurs** (till exempel ett relä namn område). Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute, VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsoft stamnätet-nätverket som eliminerar exponering från det offentliga Internet. Du kan ange en nivå av granularitet i åtkomst kontroll genom att tillåta anslutningar till vissa Azure Relay namn områden. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Lägg till en privat slut punkt med Azure Portal

### <a name="prerequisites"></a>Förutsättningar
Om du vill integrera ett Azure Relay-namnområde med en privat Azure-länk behöver du följande entiteter eller behörigheter:

- Ett Azure Relay namn område.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägar-eller deltagar behörighet för det virtuella nätverket.

Din privata slut punkt och det virtuella nätverket måste finnas i samma region. När du väljer en region för den privata slut punkten med hjälp av portalen filtreras automatiskt endast virtuella nätverk i den regionen. Ditt namn område kan finnas i en annan region.

Din privata slut punkt använder en privat IP-adress i det virtuella nätverket.

### <a name="steps"></a>Steg
Stegvisa instruktioner om hur du skapar en ny Azure Relay namnrymd och entiteter i den finns i [skapa ett Azure Relay-namnområde med hjälp av Azure Portal](relay-create-namespace-portal.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Sök fältet skriver du i **reläer**.
3. Välj det **namn område** i listan som du vill lägga till en privat slut punkt för.
4. Välj fliken **nätverk** under **Inställningar**.
5. Välj fliken **anslutningar för privata slut punkter** överst på sidan
6. Välj knappen **+ privat slut punkt** överst på sidan.

    ![Knappen Lägg till privat slut punkt](./media/private-link-service/add-private-endpoint-button.png)
7. Följ de här stegen på sidan **grundläggande** : 
    1. Välj den **Azure-prenumeration** där du vill skapa den privata slut punkten. 
    2. Välj **resurs grupp** för den privata slut punkts resursen.
    3. Ange ett **namn** för den privata slut punkten. 
    5. Välj en **region** för den privata slut punkten. Din privata slut punkt måste finnas i samma region som ditt virtuella nätverk, men kan finnas i en annan region än Azure Relay namn området som du ansluter till. 
    6. Välj **Nästa: resurs >s** knappen längst ned på sidan.

        ![Sidan skapa privat slut punkt – grunder](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Följ de här stegen på sidan **resurs** :
    1. Om du väljer **Anslut till en Azure-resurs i min katalog** med anslutnings metod har du ägar-eller deltagar åtkomst till namn området och att namn området finns i samma katalog som den privata slut punkten. Följ sedan dessa steg: 
        1. Välj den **Azure-prenumeration** där **Azure Relay namn området** finns. 
        2. För **resurs typ** väljer du **Microsoft. Relay/namnrymder** för **resurs typen**.
        3. För **resurs** väljer du ett relä namn område i den nedrullningsbara listan. 
        4. Bekräfta att **mål under resursen** har angetts till **namnrymd**.
        5. Välj **Nästa: konfiguration >s** knappen längst ned på sidan. 
        
            ![Skapa privat slut punkt – resurs sida](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Om du väljer **Anslut till en Azure-resurs efter resurs-ID eller alias** eftersom namn området inte finns i samma katalog som den privata slut punkten, följer du dessa steg:
        1. Ange **resurs-ID** eller **alias**. Det kan vara det resurs-ID eller alias som någon har delat med dig. Det enklaste sättet att hämta resurs-ID är att navigera till Azure Relay namn området i Azure Portal och kopiera del av URI: n som börjar från `/subscriptions/` . Här är ett exempel: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. För **under resurs för mål** anger du **namnrymd**. Det är den typ av under resurs som din privata slut punkt kan komma åt.
        3. valfritt Ange ett **meddelande om begäran**. Resurs ägaren ser det här meddelandet vid hantering av privat slut punkts anslutning.
        4. Välj sedan **Nästa: konfiguration >** knappen längst ned på sidan.

            ![Skapa privat slut punkt – Anslut med resurs-ID](./media/private-link-service/connect-resource-id.png)
9. På sidan **konfiguration** väljer du det undernät i ett virtuellt nätverk som du vill distribuera den privata slut punkten till. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i list rutan. 
    2. Välj ett **undernät** i det virtuella nätverk som du har valt. 
    3. Aktivera **integrering med privat DNS-zon** om du vill integrera din privata slut punkt med en privat DNS-zon. 
    
        För att kunna ansluta privat med din privata slut punkt behöver du en DNS-post. Vi rekommenderar att du integrerar din privata slut punkt med en **privat DNS-zon**. Du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av Host-filerna på dina virtuella datorer. Mer information finns i [Azures DNS-konfiguration för privat slut punkt](../private-link/private-endpoint-dns.md). I det här exemplet är alternativet **integrera med privat DNS-zon** markerat och en privat DNS-zon skapas åt dig. 
    3. Välj **Nästa: tagg >** -knappen längst ned på sidan. 

        ![Skapa privat slut punkt – konfigurations sida](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. På sidan **taggar** skapar du alla Taggar (namn och värden) som du vill koppla till den privata slut punkten och den privata DNS-zonen (om du har aktiverat alternativet). Välj sedan **Granska + skapa** längst ned på sidan. 
11. Granska alla inställningar på sidan **Granska och skapa** och välj **skapa** för att skapa den privata slut punkten.
    
    ![Skapa privat slut punkt – sidan Granska och skapa](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. På sidan **privat slut punkt** kan du se statusen för den privata slut punkts anslutningen. Om du är ägare till relä namn området eller har behörighet att hantera åtkomst över det och har valt **Anslut till en Azure-resurs i mitt katalog** alternativ för **anslutnings metoden**, ska slut punkts anslutningen **godkännas automatiskt**. Om det är i **vänte** läge, se avsnittet [hantera privata slut punkter med Azure Portal](#manage-private-endpoints-using-azure-portal) .

    ![Sidan privat slut punkt](./media/private-link-service/private-endpoint-page.png)
13. Gå tillbaka till sidan **nätverk** i **namn området** och växla till fliken anslutningar för **privat slut punkt** . Du bör se den privata slut punkten som du skapade. 

    ![Den privata slut punkten har skapats](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Lägg till en privat slut punkt med PowerShell
I följande exempel visas hur du använder Azure PowerShell för att skapa en privat slut punkts anslutning till ett Azure Relay namn område.

Din privata slut punkt och det virtuella nätverket måste finnas i samma region. Ditt Azure Relay namn område kan finnas i en annan region. Och den privata slut punkten använder en privat IP-adress i det virtuella nätverket.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Hantera privata slut punkter med hjälp av Azure Portal

När du skapar en privat slut punkt måste anslutningen godkännas. Om resursen (relä namn område) för vilken du skapar en privat slut punkt finns i din katalog, kan du godkänna anslutningsbegäran förutsatt att du har behörighet att hantera i relä namn området. Om du ansluter till ett relä namn område som du inte har behörighet att hantera, måste du vänta tills ägaren av resursen har godkänt din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Tjänståtgärd | Status för privat slut punkt för tjänst förbrukare | Beskrivning |
|--|--|--|
| Ingen | Väntar | Anslutningen skapas manuellt och väntar på godkännande från Azure Relay namn områdes ägare. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Avslagen | Anslutningen avvisades av Azure Relay namn områdes ägare. |
| Ta bort | Frånkopplad | Anslutningen togs bort av Azure Relay namn områdets ägare, den privata slut punkten blir informativ och bör tas bort för rensning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Godkänn, avvisa eller ta bort en privat slut punkts anslutning

1. Logga in på Azure-portalen.
1. I Sök fältet skriver du i **relä**.
1. Välj det **namn område** som du vill hantera.
1. Välj fliken **Nätverk**.
5. Gå till lämpligt avsnitt nedan, baserat på den åtgärd du vill: Godkänn, avvisa eller ta bort. 

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slut punkts anslutning

1. Om det finns några anslutningar som väntar, visas en anslutning som anges i **väntan** på etablerings status. 
2. Välj den **privata slut punkt** som du vill godkänna
3. Välj knappen **Godkänn** .

    ![Godkänn privat slut punkt](./media/private-link-service/private-endpoint-approve.png)
4. På sidan **Godkänn anslutning** anger du en valfri **kommentar** och väljer **Ja**. Om du väljer **Nej** händer ingenting. 

    ![Sidan Godkänn anslutning](./media/private-link-service/approve-connection-page.png)
5. Du bör se status för anslutningen i listan ändrad till **godkänd**.

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slut punkts anslutning

1. Om det finns anslutningar för privata slut punkter som du vill avvisa, oavsett om det är en väntande begäran eller en befintlig anslutning som har godkänts tidigare, väljer du slut punkts anslutningen och klickar på knappen **avvisa** .

    ![Knappen avvisa](./media/private-link-service/private-endpoint-reject.png)
2. På sidan **avvisa anslutning** anger du en valfri kommentar och väljer **Ja**. Om du väljer **Nej** händer ingenting. 

    ![Sidan avvisa anslutning](./media/private-link-service/reject-connection-page.png)
3. Du bör se status för anslutningen i listan ändrad **avvisad**.


### <a name="remove-a-private-endpoint-connection"></a>Ta bort en privat slut punkts anslutning

1. Om du vill ta bort en privat slut punkts anslutning markerar du den i listan och väljer **ta bort** i verktygsfältet. 

    ![Knappen Ta bort](./media/private-link-service/remove-endpoint.png)
2. På sidan **ta bort anslutning** väljer du **Ja** för att bekräfta borttagningen av den privata slut punkten. Om du väljer **Nej** händer ingenting. 

    ![Sidan ta bort anslutning](./media/private-link-service/delete-connection-page.png)
3. Du bör se statusen ändrad till **frånkopplad**. Sedan visas slut punkten kvar från listan. 

## <a name="validate-that-the-private-link-connection-works"></a>Kontrol lera att anslutningen till den privata länken fungerar
Du bör kontrol lera att resurserna i det virtuella nätverket för den privata slut punkten ansluter till Azure Relay namn området via dess privata IP-adress.

För det här testet skapar du en virtuell dator genom att följa stegen i [skapa en virtuell Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md)

På fliken **nätverk** : 

1. Ange **virtuellt nätverk** och **undernät**. Du måste välja den Virtual Network som du har distribuerat den privata slut punkten till.
2. Ange en **offentlig IP-** resurs.
3. Välj **ingen** för nätverkskortets **nätverks säkerhets grupp**.
4. För **belastnings utjämning** väljer du **Nej**.

Anslut till den virtuella datorn och öppna kommando raden och kör följande kommando:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Du bör se ett resultat som ser ut ungefär så här. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Begränsningar och design överväganden

### <a name="design-considerations"></a>Designöverväganden
- Information om priser finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Begränsningar 
- Maximalt antal privata slut punkter per Azure Relay namnrymd: 64.
- Maximalt antal Azure Relay namn rymder med privata slut punkter per prenumeration: 64.
- Regler för nätverks säkerhets grupper (NSG) och User-Defined vägar gäller inte för privat slut punkt. Mer information finns i [Azure Private Link service: begränsningar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azures privata länk](../private-link/private-link-service-overview.md)
- Läs mer om [Azure Relay](relay-what-is-it.md)
