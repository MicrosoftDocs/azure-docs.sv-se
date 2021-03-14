---
title: PowerShell-skript med AZ. search-modulen
titleSuffix: Azure Cognitive Search
description: Skapa och konfigurera en Azure Kognitiv sökning-tjänst med PowerShell. Du kan skala upp eller ned en tjänst, hantera administratörs-och fråge-API-nycklar och fråga efter system information.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: efb5d498c627a6731d2a90623c81eefabd0042a0
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462787"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Hantera Azure Kognitiv sökning-tjänsten med PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Du kan köra PowerShell-cmdlets och skript i Windows, Linux eller i [Azure Cloud Shell](../cloud-shell/overview.md) för att skapa och konfigurera Azure kognitiv sökning. **AZ. search** -modulen utökar [Azure PowerShell](/powershell/) med fullständig paritet till [Sök hantering REST-API: er](/rest/api/searchmanagement) och möjlighet att utföra följande uppgifter:

> [!div class="checklist"]
> * [Visa en lista över Sök tjänster i en prenumeration](#list-search-services)
> * [Information om tjänst utbyte](#get-search-service-information)
> * [Skapa eller ta bort en tjänst](#create-or-delete-a-service)
> * [Skapa en tjänst med en privat slut punkt](#create-a-service-with-a-private-endpoint)
> * [Återskapa Admin-API – nycklar](#regenerate-admin-keys)
> * [Skapa eller ta bort fråge-API – nycklar](#create-or-delete-query-keys)
> * [Skala upp eller ned med repliker och partitioner](#scale-replicas-and-partitions)
> * [Skapa en delad privat länk resurs](#create-a-shared-private-link-resource)

Ibland tillfrågas frågor om uppgifter som *inte* finns i listan ovan. För närvarande kan du inte använda antingen **AZ. search** -modulen eller hanterings REST API för att ändra server namn, region eller nivå. Dedikerade resurser allokeras när en tjänst skapas. Därför kräver ändring av den underliggande maskin varan (plats eller nodtyp) en ny tjänst. På samma sätt finns det inga verktyg eller API: er för att överföra innehåll, till exempel ett index, från en tjänst till en annan.

I en tjänst är skapandet och hanteringen av innehåll via [Search Service REST API](/rest/api/searchservice/) eller [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Det finns inga dedikerade PowerShell-kommandon för innehåll, men du kan skriva PowerShell-skript som anropar REST-eller .NET-API: er för att skapa och läsa in index.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Kontrol lera versioner och läsa in moduler

Exemplen i den här artikeln är interaktiva och kräver förhöjd behörighet. Azure PowerShell ( **AZ** -modulen) måste vara installerad. Mer information finns i [installera Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>PowerShell-versions kontroll (5,1 eller senare)

Lokal PowerShell måste vara 5,1 eller senare på alla operativ system som stöds.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Läs in Azure PowerShell

Om du inte är säker på om **AZ** har installerats kör du följande kommando som ett verifierings steg. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Vissa system laddar inte upp moduler automatiskt. Om du får ett fel meddelande om föregående kommando kan du försöka läsa in modulen och om det inte går går du tillbaka till installations anvisningarna för att se om du har missat ett steg.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Ansluta till Azure med en webbläsares inloggnings-token

Du kan använda dina inloggnings uppgifter för din portal för att ansluta till en prenumeration i PowerShell. Alternativt kan du [autentisera icke-interaktivt med ett huvud namn för tjänsten](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Om du har flera Azure-prenumerationer ställer du in din Azure-prenumeration. Kör det här kommandot om du vill se en lista över dina aktuella prenumerationer.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Kör följande kommando för att ange prenumerationen. I följande exempel är prenumerations namnet `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Visa en lista över tjänster i en prenumeration

Följande kommandon är från [**AZ. Resources**](/powershell/module/az.resources), returnerar information om befintliga resurser och tjänster som redan har skapats i din prenumeration. Om du inte vet hur många Sök tjänster som redan har skapats, returnerar de här kommandona informationen och sparar en resa till portalen.

Det första kommandot returnerar alla Sök tjänster.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Returnera information om en angiven resurs i listan över tjänster.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Resultaten bör likna följande utdata.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importera AZ. search

Kommandon från [**AZ. search**](/powershell/module/az.search) är inte tillgängligt förrän du har läst in modulen.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Visa alla AZ. search-kommandon

Som ett verifierings steg returnerar du en lista med kommandon som finns i modulen.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Resultaten bör likna följande utdata.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Om du har en äldre version av paketet uppdaterar du modulen för att få de senaste funktionerna.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Hämta information om Sök tjänst

Efter **AZ. search** har importer ATS och du känner till resurs gruppen som innehåller din Sök tjänst, kör [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) för att returnera tjänst definitionen, inklusive namn, region, nivå och replikering och antal partitioner.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Resultaten bör likna följande utdata.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Skapa eller ta bort en tjänst

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchservice) används för att [skapa en ny Sök tjänst](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
Resultaten bör likna följande utdata.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>Skapa en tjänst med IP-regler

Beroende på dina säkerhets krav kanske du vill skapa en Sök tjänst med en [konfigurerad IP-brandvägg](service-configure-firewall.md). Det gör du genom att först definiera IP-reglerna och sedan skicka dem till `IPRuleList` parametern enligt nedan.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Skapa en tjänst med en systemtilldelad hanterad identitet

I vissa fall, till exempel när du [använder hanterad identitet för att ansluta till en data källa](search-howto-managed-identities-storage.md), måste du aktivera [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md). Detta görs genom att lägga till `-IdentityType SystemAssigned` i kommandot.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Skapa en tjänst med en privat slut punkt

[Privata slut punkter](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data i ett Sök index över en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det [virtuella nätverkets adress utrymme](../virtual-network/private-ip-addresses.md) för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Mer information finns i dokumentationen om hur du [skapar en privat slut punkt för Azure kognitiv sökning](service-create-private-endpoint.md)

I följande exempel visas hur du skapar en Sök tjänst med en privat slut punkt.

Distribuera först en Sök tjänst med `PublicNetworkAccess` set till `Disabled` .

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Skapa sedan ett virtuellt nätverk, en privat nätverks anslutning och den privata slut punkten.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Skapa slutligen en privat DNS-zon. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

Mer information om hur du skapar privata slut punkter i PowerShell finns i den här snabb starten för [privata länkar](../private-link/create-private-endpoint-powershell.md)

### <a name="manage-private-endpoint-connections"></a>Hantera anslutningar för privata slut punkter

Förutom att skapa en privat slut punkts anslutning kan du också `Get` , `Set` och `Remove` anslutningen.

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) används för att hämta en privat slut punkts anslutning och se dess status.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) används för att uppdatera anslutningen. I följande exempel anges en privat slut punkts anslutning till avvisad:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) används för att ta bort den privata slut punkts anslutningen.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Återskapa administratörs nycklar

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) används för att rulla över administrations- [API-nycklar](search-security-api-keys.md). Två administratörs nycklar skapas med varje tjänst för autentiserad åtkomst. Nycklar krävs på varje begäran. Båda administratörs nycklarna fungerar som likvärdiga, vilket ger fullständig Skriv behörighet till en Sök tjänst med möjlighet att hämta information eller skapa och ta bort objekt. Det finns två nycklar så att du kan använda en när du ersätter den andra. 

Du kan bara återskapa en i taget, som antingen har angetts som `primary` eller- `secondary` nyckeln. Kom ihåg att uppdatera all klient kod för att använda en sekundär nyckel när du rullar över primär nyckeln för en oavbruten tjänst. Undvik att ändra nycklarna när åtgärderna är i flygning.

Om du återskapar nycklar utan att uppdatera klient koden, kan det hända att förfrågningar som använder den gamla nyckeln Miss förväntas. Om du återskapar alla nya nycklar låses du inte permanent av tjänsten och du kan fortfarande komma åt tjänsten via portalen. När du har återskapat primära och sekundära nycklar kan du uppdatera klient koden för att använda de nya nycklarna och åtgärderna kommer att återupptas på motsvarande sätt.

Värdena för API-nycklarna genereras av tjänsten. Du kan inte ange en anpassad nyckel för Azure Kognitiv sökning att använda. Det finns på liknande sätt inget användardefinierat namn för Admin API-nycklar. Referenser till nyckeln är fasta strängar, antingen `primary` eller `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Resultaten bör likna följande utdata. Båda nycklarna returneras även om du bara ändrar en i taget.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Skapa eller ta bort frågeinställningar

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) används för att skapa fråge- [API-nycklar](search-security-api-keys.md) för skrivskyddad åtkomst från klient program till ett Azure kognitiv sökning-index. Frågeinställningar används för att autentisera till ett specifikt index i syfte att hämta Sök resultat. Frågeinställningar beviljar inte skrivskyddad åtkomst till andra objekt i tjänsten, till exempel ett index, en data källa eller indexerare.

Det går inte att ange en nyckel för Azure Kognitiv sökning att använda. API-nycklar genereras av tjänsten.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skala repliker och partitioner

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) används för att [öka eller minska repliker och partitioner](search-capacity-planning.md) för att justera fakturerbara resurser i tjänsten. Ökning av repliker eller partitioner läggs till på din faktura, som har både fasta och variabla avgifter. Om du har tillfälligt behov av ytterligare processor kraft kan du öka replikeringen och partitionerna för att hantera arbets belastningen. Övervaknings avsnittet på sidan översikts Portal innehåller paneler för svars tid, frågor per sekund och begränsning, vilket anger om den aktuella kapaciteten är tillräcklig.

Det kan ta en stund att lägga till eller ta bort en omkälla. Justeringar av kapaciteten sker i bakgrunden, vilket gör att befintliga arbets belastningar kan fortsätta. Ytterligare kapacitet används för inkommande begär Anden så snart det är klart, utan ytterligare konfiguration krävs. 

Borttagning av kapacitet kan vara störande. Att stoppa alla indexerings-och index jobb innan du minskar kapaciteten rekommenderas för att undvika förlorade begär Anden. Om det inte är möjligt kan du överväga att minska kapaciteten stegvis, en replik och partition i taget, tills de nya mål nivåerna har nåtts.

När du har skickat kommandot finns det inget sätt att avsluta det halvvägs. Du måste vänta tills kommandot har avslut ATS innan du kan ändra antalet.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Resultaten bör likna följande utdata.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Skapa en delad privat länk resurs

Privata slut punkter för skyddade resurser som skapas via Azure Kognitiv sökning API: er kallas *delade privata länk resurser*. Detta beror på att du är "delning"-åtkomst till en resurs, till exempel ett lagrings konto, som har integrerats med [Azure Private Link-tjänsten](https://azure.microsoft.com/services/private-link/).

Om du använder en indexerare för att indexera data i Azure Kognitiv sökning och data källan finns i ett privat nätverk kan du skapa en utgående [privat slut punkts anslutning](../private-link/private-endpoint-overview.md) för att komma åt data.

En fullständig lista över Azure-resurser för vilka du kan skapa utgående privata slut punkter från Azure Kognitiv sökning finns [här](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) tillsammans med de relaterade **grupp-ID-** värdena.

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) används för att skapa en delad privat länk resurs. Tänk på att en viss konfiguration kan krävas för data källan innan du kör det här kommandot.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

Med [Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) kan du hämta de delade privata länk resurserna och se deras status.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Du måste godkänna anslutningen med följande kommando innan den kan användas.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) används för att ta bort den delade privata länk resursen.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

Fullständig information om hur du konfigurerar delade privata länk resurser finns i dokumentationen om [att skapa index anslutningar via en privat slut punkt](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Nästa steg

Bygg ett [index](search-what-is-an-index.md), [fråga ett index](search-query-overview.md) med hjälp av portalen, REST-API: er eller .NET SDK.

* [Skapa ett Azure Kognitiv sökning-index i Azure Portal](search-get-started-portal.md)
* [Konfigurera en indexerare för att läsa in data från andra tjänster](search-indexer-overview.md)
* [Fråga ett Azure Kognitiv sökning-index med hjälp av Sök Utforskaren i Azure Portal](search-explorer.md)
* [Använda Azure Kognitiv sökning i .NET](search-howto-dotnet-sdk.md)
