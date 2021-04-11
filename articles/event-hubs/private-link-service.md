---
title: Integrera Azure Event Hubs med Azure Private Link service
description: Lär dig hur du integrerar Azure Event Hubs med Azure Private Link service
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: f5c01788044f3c3a5d875a24172e7222ff195f81
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960851"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Tillåt åtkomst till Azure Event Hubs-namnrymder via privata slut punkter 
Azure Private Link service ger dig åtkomst till Azure-tjänster (till exempel Azure Event Hubs, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partner tjänster via en **privat slut punkt** i det virtuella nätverket.

En privat slut punkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från det virtuella nätverket, vilket effektivt ansluter tjänsten till det virtuella nätverket. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Mer information finns i [Vad är en privat Azure-länk?](../private-link/private-link-overview.md)

## <a name="important-points"></a>Viktiga punkter
- Den här funktionen stöds för både **standard** -och **dedikerade** nivåer. Det stöds inte på **Basic** -nivån.
- Att aktivera privata slut punkter kan förhindra att andra Azure-tjänster interagerar med Event Hubs.  Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare. Som ett undantag kan du tillåta åtkomst till Event Hubs resurser från vissa **betrodda tjänster** även när privata slut punkter är aktiverade. En lista över betrodda tjänster finns i [betrodda tjänster](#trusted-microsoft-services).
- Ange **minst en IP-regel eller en regel för virtuella nätverk** för namn området för att tillåta trafik enbart från de angivna IP-adresserna eller under nätet för ett virtuellt nätverk. Om det inte finns några IP-och virtuella nätverks regler kan namn området nås via det offentliga Internet (med hjälp av åtkomst nyckeln). 

## <a name="add-a-private-endpoint-using-azure-portal"></a>Lägg till en privat slut punkt med Azure Portal

### <a name="prerequisites"></a>Förutsättningar

Om du vill integrera ett Event Hubs-namnområde med en privat Azure-länk behöver du följande entiteter eller behörigheter:

- Ett Event Hubs namn område.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket. Du kan använda **standard** under nätet. 
- Ägar-eller deltagar behörigheter för både namn området och det virtuella nätverket.

Din privata slut punkt och det virtuella nätverket måste finnas i samma region. När du väljer en region för den privata slut punkten med hjälp av portalen filtreras automatiskt endast virtuella nätverk i den regionen. Ditt namn område kan finnas i en annan region.

Din privata slut punkt använder en privat IP-adress i det virtuella nätverket.

### <a name="steps"></a>Steg
Om du redan har ett Event Hubs namn område kan du skapa en privat länk anslutning genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Sök fältet skriver du i **Event Hub**.
3. Välj det **namn område** i listan som du vill lägga till en privat slut punkt för.
4. Välj **nätverk** under **Inställningar** på den vänstra menyn.

    > [!NOTE]
    > Fliken **nätverk** visas endast för **standard** -eller **dedikerade** namn områden. 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Fliken nätverk – alternativet valda nätverk" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!WARNING]
    > Som standard är alternativet **valda nätverk** markerat. Om du inte anger en IP-brandväggsregel eller lägger till ett virtuellt nätverk kan namn området nås via offentliga Internet (med hjälp av åtkomst nyckeln). 
1. Välj fliken **anslutningar för privata slut punkter** överst på sidan. 
1. Välj knappen **+ privat slut punkt** överst på sidan.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Sidan nätverk – fliken anslutningar för privata slut punkter – Lägg till privat slut punkt länk":::
7. Följ de här stegen på sidan **grundläggande** : 
    1. Välj den **Azure-prenumeration** där du vill skapa den privata slut punkten. 
    2. Välj **resurs grupp** för den privata slut punkts resursen.
    3. Ange ett **namn** för den privata slut punkten. 
    5. Välj en **region** för den privata slut punkten. Din privata slut punkt måste finnas i samma region som ditt virtuella nätverk, men kan finnas i en annan region än den privata länk resurs som du ansluter till. 
    6. Välj **Nästa: resurs >s** knappen längst ned på sidan.

        ![Sidan skapa privat slut punkt – grunder](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Följ de här stegen på sidan **resurs** :
    1. För anslutnings metod, om du väljer **Anslut till en Azure-resurs i min katalog**, följer du dessa steg: 
        1. Välj den **Azure-prenumeration** där **Event Hubs namn området** finns. 
        2. För **resurs typ** väljer du **Microsoft. EventHub/Namespaces** för **resurs typen**.
        3. För **resurs** väljer du ett Event Hubs namn område i list rutan. 
        4. Bekräfta att **mål under resursen** har angetts till **namnrymd**.
        5. Välj **Nästa: konfiguration >s** knappen längst ned på sidan. 
        
            ![Skapa privat slut punkt – resurs sida](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Om du väljer **Anslut till en Azure-resurs efter resurs-ID eller alias** följer du dessa steg:
        1. Ange **resurs-ID** eller **alias**. Det kan vara det resurs-ID eller alias som någon har delat med dig. Det enklaste sättet att hämta resurs-ID är att navigera till Event Hubs namn området i Azure Portal och kopiera del av URI: n som börjar från `/subscriptions/` . Se följande bild för ett exempel. 
        2. För **under resurs för mål** anger du **namnrymd**. Det är den typ av under resurs som din privata slut punkt kan komma åt.
        3. valfritt Ange ett **meddelande om begäran**. Resurs ägaren ser det här meddelandet vid hantering av privat slut punkts anslutning.
        4. Välj sedan **Nästa: konfiguration >** knappen längst ned på sidan.

            ![Skapa privat slut punkt – Anslut med resurs-ID](./media/private-link-service/connect-resource-id.png)
9. På sidan **konfiguration** väljer du det undernät i ett virtuellt nätverk som du vill distribuera den privata slut punkten till. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i list rutan. 
    2. Välj ett **undernät** i det virtuella nätverk som du har valt. 
    3. Välj **Nästa: tagg >** -knappen längst ned på sidan. 

        ![Skapa privat slut punkt – konfigurations sida](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. På sidan **taggar** skapar du alla Taggar (namn och värden) som du vill koppla till den privata slut punkts resursen. Välj sedan **Granska + skapa** längst ned på sidan. 
11. Granska alla inställningar på sidan **Granska och skapa** och välj **skapa** för att skapa den privata slut punkten.
    
    ![Skapa privat slut punkt – sidan Granska och skapa](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Bekräfta att du ser den privata slut punkts anslutningen som du skapade visas i listan över slut punkter. I det här exemplet godkänns den privata slut punkten automatiskt eftersom du anslöt till en Azure-resurs i din katalog och du har tillräcklig behörighet. 

    ![Den privata slut punkten har skapats](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Om du vill tillåta att betrodda tjänster får åtkomst till ditt namn område växlar du till fliken **brand väggar och virtuella nätverk** på sidan **nätverk** och väljer **Ja** för **Tillåt betrodda Microsoft-tjänster för att kringgå den här brand väggen?**. 

## <a name="add-a-private-endpoint-using-powershell"></a>Lägg till en privat slut punkt med PowerShell
I följande exempel visas hur du använder Azure PowerShell för att skapa en privat slut punkts anslutning. Det skapar inte ett dedikerat kluster åt dig. Följ stegen i [den här artikeln](event-hubs-dedicated-cluster-create-portal.md) för att skapa ett dedikerat Event Hubs-kluster. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen
Skapa en privat DNS-zon för Event Hubs domän och skapa en kopplings länk med det virtuella nätverket:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Hantera privata slut punkter med hjälp av Azure Portal

När du skapar en privat slut punkt måste anslutningen godkännas. Om den resurs som du skapar en privat slut punkt för finns i din katalog kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta tills ägaren av resursen har godkänt din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Tjänståtgärd | Status för privat slut punkt för tjänst förbrukare | Beskrivning |
|--|--|--|
| Ingen | Väntar | Anslutningen skapas manuellt och väntar på godkännande från ägaren till den privata länk resursen. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Avslagen | Anslutningen avvisades av ägaren till den privata länk resursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länk resursen, den privata slut punkten blir informativ och bör tas bort för rensning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Godkänn, avvisa eller ta bort en privat slut punkts anslutning

1. Logga in på Azure-portalen.
2. I Sök fältet skriver du i **Event Hub**.
3. Välj det **namn område** som du vill hantera.
4. Välj fliken **Nätverk**.
5. Gå till lämpligt avsnitt nedan, baserat på den åtgärd du vill: Godkänn, avvisa eller ta bort.

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slut punkts anslutning
1. Om det finns några anslutningar som väntar, visas en anslutning som anges i **väntan** på etablerings status. 
2. Välj den **privata slut punkt** som du vill godkänna
3. Välj knappen **Godkänn** .

    ![Godkänn privat slut punkt](./media/private-link-service/approve-private-endpoint.png)
4. På sidan **Godkänn anslutning** lägger du till en kommentar (valfritt) och väljer **Ja**. Om du väljer **Nej** händer ingenting. 
5. Du bör se statusen för den privata slut punkts anslutningen i listan ändrad till **godkänd**. 

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slut punkts anslutning

1. Om det finns anslutningar för privata slut punkter som du vill avvisa, oavsett om det är en väntande begäran eller en befintlig anslutning, väljer du anslutningen och klickar på knappen **avvisa** .

    ![Avvisa privat slut punkt](./media/private-link-service/private-endpoint-reject-button.png)
2. På sidan **avvisa anslutning** anger du en kommentar (valfritt) och väljer **Ja**. Om du väljer **Nej** händer ingenting. 
3. Du bör se statusen för den privata slut punkts anslutningen i listan ändrad till **avvisad**. 

### <a name="remove-a-private-endpoint-connection"></a>Ta bort en privat slut punkts anslutning

1. Om du vill ta bort en privat slut punkts anslutning markerar du den i listan och väljer **ta bort** i verktygsfältet.
2. På sidan **ta bort anslutning** väljer du **Ja** för att bekräfta borttagningen av den privata slut punkten. Om du väljer **Nej** händer ingenting.
3. Du bör se statusen ändrad till **frånkopplad**. Sedan visas slut punkten kvar från listan.

## <a name="validate-that-the-private-link-connection-works"></a>Kontrol lera att anslutningen till den privata länken fungerar

Du bör kontrol lera att resurserna i det virtuella nätverket för den privata slut punkten ansluter till Event Hubs namn området över en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Börja med att skapa en virtuell dator genom att följa stegen i [skapa en virtuell Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md)

På fliken **nätverk** : 

1. Ange **virtuellt nätverk** och **undernät**. Du måste välja den Virtual Network som du har distribuerat den privata slut punkten till.
2. Ange en **offentlig IP-** resurs.
3. Välj **ingen** för nätverkskortets **nätverks säkerhets grupp**.
4. För **belastnings utjämning** väljer du **Nej**.

Anslut till den virtuella datorn, öppna kommando raden och kör följande kommando:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Du bör se ett resultat som ser ut ungefär så här. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Begränsningar och design överväganden

**Priser**: information om priser finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/).

**Begränsningar**: den här funktionen är tillgänglig i alla offentliga Azure-regioner.

**Maximalt antal privata slut punkter per Event Hubs namnrymd**: 120.

Mer information finns i [Azure Private Link service: begränsningar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azures privata länk](../private-link/private-link-service-overview.md)
- Läs mer om [Azure Event Hubs](event-hubs-about.md)
