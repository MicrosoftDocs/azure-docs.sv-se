---
title: Azure CLI-skript med hjälp av sökmodulen AZ
titleSuffix: Azure Cognitive Search
description: Skapa och konfigurera en Azure Kognitiv sökning-tjänst med Azure CLI. Du kan skala upp eller ned en tjänst, hantera administratörs-och fråge-API-nycklar och fråga efter system information.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: ee6b0e1b745e86c72843af88c0f6d17f91512e15
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176764"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Hantera Azure Kognitiv sökning-tjänsten med Azure CLI
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Du kan köra Azure CLI-kommandon och skript på Windows, macOS, Linux eller i [Azure Cloud Shell](../cloud-shell/overview.md) för att skapa och konfigurera Azure kognitiv sökning. Sökmodulen [**AZ**](/cli/azure/search) utökar [Azure CLI](/cli/) med fullständig paritet till [Sök hantering REST-API: er](/rest/api/searchmanagement) och möjlighet att utföra följande uppgifter:

> [!div class="checklist"]
> * [Visa en lista över Sök tjänster i en prenumeration](#list-search-services)
> * [Information om tjänst utbyte](#get-search-service-information)
> * [Skapa eller ta bort en tjänst](#create-or-delete-a-service)
> * [Skapa en tjänst med en privat slut punkt](#create-a-service-with-a-private-endpoint)
> * [Återskapa Admin-API – nycklar](#regenerate-admin-keys)
> * [Skapa eller ta bort fråge-API – nycklar](#create-or-delete-query-keys)
> * [Skala upp eller ned med repliker och partitioner](#scale-replicas-and-partitions)
> * [Skapa en delad privat länk resurs](#create-a-shared-private-link-resource)

Ibland tillfrågas frågor om uppgifter som *inte* finns i listan ovan. För närvarande kan du inte använda antingen **AZ** sökmodul eller hanterings REST API för att ändra ett server namn, en region eller en nivå. Dedikerade resurser allokeras när en tjänst skapas. Därför kräver ändring av den underliggande maskin varan (plats eller nodtyp) en ny tjänst. På samma sätt finns det inga verktyg eller API: er för att överföra innehåll, till exempel ett index, från en tjänst till en annan.

I en tjänst är skapandet och hanteringen av innehåll via [Search Service REST API](/rest/api/searchservice/) eller [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Det finns inga dedikerade PowerShell-kommandon för innehåll, men du kan skriva skript som anropar REST-eller .NET-API: er för att skapa och läsa in index.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Visa en lista över tjänster i en prenumeration

Följande kommandon är från [**AZ Resource**](/cli/azure/resource)och returnerar information om befintliga resurser och tjänster som redan har skapats i din prenumeration. Om du inte vet hur många Sök tjänster som redan har skapats, returnerar de här kommandona informationen och sparar en resa till portalen.

Det första kommandot returnerar alla Sök tjänster.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Returnera information om en angiven resurs i listan över tjänster.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Visa alla AZ search-kommandon

Du kan visa information om de under grupper och kommandon som är tillgängliga i [**AZ-sökning**](/cli/azure/search) inifrån cli. Du kan också läsa [dokumentationen](/cli/azure/search).

Om du vill visa de under grupper som är tillgängliga i `az search` kör du följande kommando.

```azurecli-interactive
az search --help
```

Svaret bör se ut ungefär som i följande utdata.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

I varje under grupp är flera kommandon tillgängliga. Du kan se de tillgängliga kommandona för under `service` gruppen genom att köra följande rad.

```azurecli-interactive
az search service --help
```

Du kan också se de argument som är tillgängliga för ett visst kommando.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Hämta information om Sök tjänst

Om du känner till resurs gruppen som innehåller din Sök tjänst kan du köra [**AZ Search-tjänsten**](/cli/azure/search/service#az_search_service_show) för att returnera tjänst definitionen, inklusive namn, region, nivå och replikering och antal partitioner.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Skapa eller ta bort en tjänst

Om du vill [skapa en ny Sök tjänst](search-create-service-portal.md)använder du kommandot [**AZ search service Create**](/cli/azure/search/service#az_search_service_show) .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Resultaten bör likna följande utdata:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Skapa en tjänst med IP-regler

Beroende på dina säkerhets krav kanske du vill skapa en Sök tjänst med en [konfigurerad IP-brandvägg](service-configure-firewall.md). Det gör du genom att skicka de offentliga IP-adresserna (v4) eller CIDR-intervall till `ip-rules` argumentet som visas nedan. Regler ska avgränsas med kommatecken ( `,` ) eller semikolon ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Skapa en tjänst med en systemtilldelad hanterad identitet

I vissa fall, till exempel när du [använder hanterad identitet för att ansluta till en data källa](search-howto-managed-identities-storage.md), måste du aktivera [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md). Detta görs genom att lägga till `--identity-type SystemAssigned` i kommandot.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Skapa en tjänst med en privat slut punkt

[Privata slut punkter](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data i ett Sök index över en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det [virtuella nätverkets adress utrymme](../virtual-network/private-ip-addresses.md) för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Mer information finns i dokumentationen om hur du [skapar en privat slut punkt för Azure kognitiv sökning](service-create-private-endpoint.md)

I följande exempel visas hur du skapar en Sök tjänst med en privat slut punkt.

Distribuera först en Sök tjänst med `PublicNetworkAccess` set till `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Skapa sedan ett virtuellt nätverk och den privata slut punkten.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Skapa slutligen en privat DNS-zon. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Mer information om hur du skapar privata slut punkter i PowerShell finns i den här snabb starten för [privata länkar](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli)

### <a name="manage-private-endpoint-connections"></a>Hantera anslutningar för privata slut punkter

Förutom att skapa en privat slut punkts anslutning kan du också `show` , `update` och `delete` anslutningen.

Om du vill hämta en privat slut punkts anslutning och se dess status använder du [**AZ search Private-Endpoint-Connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Om du vill uppdatera anslutningen använder du [**AZ search privat-Endpoint – anslutnings uppdatering**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). I följande exempel anges en privat slut punkts anslutning till avvisad:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Om du vill ta bort den privata slut punkts anslutningen använder du [**AZ search privat-slut punkt-ta bort anslutning**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Återskapa administratörs nycklar

Om du vill rulla över administrations- [API-nycklar](search-security-api-keys.md)använder du [**AZ search admin-Key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Två administratörs nycklar skapas med varje tjänst för autentiserad åtkomst. Nycklar krävs på varje begäran. Båda administratörs nycklarna fungerar som likvärdiga, vilket ger fullständig Skriv behörighet till en Sök tjänst med möjlighet att hämta information eller skapa och ta bort objekt. Det finns två nycklar så att du kan använda en när du ersätter den andra. 

Du kan bara återskapa en i taget, som antingen har angetts som `primary` eller- `secondary` nyckeln. Kom ihåg att uppdatera all klient kod för att använda en sekundär nyckel när du rullar över primär nyckeln för en oavbruten tjänst. Undvik att ändra nycklarna när åtgärderna är i flygning.

Om du återskapar nycklar utan att uppdatera klient koden, kan det hända att förfrågningar som använder den gamla nyckeln Miss förväntas. Om du återskapar alla nya nycklar låses du inte permanent av tjänsten och du kan fortfarande komma åt tjänsten via portalen. När du har återskapat primära och sekundära nycklar kan du uppdatera klient koden för att använda de nya nycklarna och åtgärderna kommer att återupptas på motsvarande sätt.

Värdena för API-nycklarna genereras av tjänsten. Du kan inte ange en anpassad nyckel för Azure Kognitiv sökning att använda. Det finns på liknande sätt inget användardefinierat namn för Admin API-nycklar. Referenser till nyckeln är fasta strängar, antingen `primary` eller `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Resultaten bör likna följande utdata. Båda nycklarna returneras även om du bara ändrar en i taget.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Skapa eller ta bort frågeinställningar

Om du vill skapa frågor och [API-nycklar](search-security-api-keys.md) för skrivskyddad åtkomst från klient program till ett Azure kognitiv sökning-index använder du [**AZ Sök fråga-nyckeln Create**](/cli/azure/search/query-key#az_search_query_key_create). Frågeinställningar används för att autentisera till ett specifikt index i syfte att hämta Sök resultat. Frågeinställningar beviljar inte skrivskyddad åtkomst till andra objekt i tjänsten, till exempel ett index, en data källa eller indexerare.

Det går inte att ange en nyckel för Azure Kognitiv sökning att använda. API-nycklar genereras av tjänsten.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Skala repliker och partitioner

Om du vill [öka eller minska repliker och partitioner](search-capacity-planning.md) använder du [**AZ Sök tjänst uppdatering**](/cli/azure/search/service#az_search_service_update). Ökning av repliker eller partitioner läggs till på din faktura, som har både fasta och variabla avgifter. Om du har tillfälligt behov av ytterligare processor kraft kan du öka replikeringen och partitionerna för att hantera arbets belastningen. Övervaknings avsnittet på sidan översikts Portal innehåller paneler för svars tid, frågor per sekund och begränsning, vilket anger om den aktuella kapaciteten är tillräcklig.

Det kan ta en stund att lägga till eller ta bort en omkälla. Justeringar av kapaciteten sker i bakgrunden, vilket gör att befintliga arbets belastningar kan fortsätta. Ytterligare kapacitet används för inkommande begär Anden så snart det är klart, utan ytterligare konfiguration krävs. 

Borttagning av kapacitet kan vara störande. Att stoppa alla indexerings-och index jobb innan du minskar kapaciteten rekommenderas för att undvika förlorade begär Anden. Om det inte är möjligt kan du överväga att minska kapaciteten stegvis, en replik och partition i taget, tills de nya mål nivåerna har nåtts.

När du har skickat kommandot finns det inget sätt att avsluta det halvvägs. Du måste vänta tills kommandot har avslut ATS innan du kan ändra antalet.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Förutom att uppdatera replikeringen och antalet partitioner kan du också uppdatera `ip-rules` , `public-access` och `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Skapa en delad privat länk resurs

Privata slut punkter för skyddade resurser som skapas via Azure Kognitiv sökning API: er kallas *delade privata länk resurser*. Detta beror på att du är "delning"-åtkomst till en resurs, till exempel ett lagrings konto, som har integrerats med [Azure Private Link-tjänsten](https://azure.microsoft.com/services/private-link/).

Om du använder en indexerare för att indexera data i Azure Kognitiv sökning och data källan finns i ett privat nätverk kan du skapa en utgående [privat slut punkts anslutning](../private-link/private-endpoint-overview.md) för att komma åt data.

En fullständig lista över Azure-resurser för vilka du kan skapa utgående privata slut punkter från Azure Kognitiv sökning finns [här](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) tillsammans med de relaterade **grupp-ID-** värdena.

Om du vill skapa en delad privat länk-resurs använder du [**AZ search Shared-Private-Link-resurs Create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Tänk på att en viss konfiguration kan krävas för data källan innan du kör det här kommandot.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Om du vill hämta de delade privata länk resurserna och visa deras status använder du [**AZ search Shared-Private-Link-Resource List**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Du måste godkänna anslutningen med följande kommando innan den kan användas. ID: t för den privata slut punkts anslutningen måste hämtas från den underordnade resursen. I det här fallet får vi anslutnings-ID från AZ-lagring.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Om du vill ta bort den delade privata länk resursen använder du [**AZ search Shared-Private-Link-resurs Delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Fullständig information om hur du konfigurerar delade privata länk resurser finns i dokumentationen om [att skapa index anslutningar via en privat slut punkt](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Nästa steg

Bygg ett [index](search-what-is-an-index.md), [fråga ett index](search-query-overview.md) med hjälp av portalen, REST-API: er eller .NET SDK.

* [Skapa ett Azure Kognitiv sökning-index i Azure Portal](search-get-started-portal.md)
* [Konfigurera en indexerare för att läsa in data från andra tjänster](search-indexer-overview.md)
* [Fråga ett Azure Kognitiv sökning-index med hjälp av Sök Utforskaren i Azure Portal](search-explorer.md)
* [Använda Azure Kognitiv sökning i .NET](search-howto-dotnet-sdk.md)
