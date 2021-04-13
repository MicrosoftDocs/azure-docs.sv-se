---
title: Hanterade virtuella & hanterade privata slutpunkter
description: Lär dig mer om hanterade virtuella nätverk och hanterade privata slutpunkter i Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: d777588f0abdd1f771deb259c597f6407e61d874
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364625"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory Managed Virtual Network (förhandsversion)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln förklarar hanterade Virtual Network och hanterade privata slutpunkter i Azure Data Factory.


## <a name="managed-virtual-network"></a>Hanterat virtuellt nätverk

När du skapar en Azure Integration Runtime (IR) i Azure Data Factory Managed Virtual Network (VNET) etableras integreringskörningen med den hanterade Virtual Network och använder privata slutpunkter för att på ett säkert sätt ansluta till datalager som stöds. 

Att skapa Azure IR i hanterade Virtual Network säkerställer att dataintegreringsprocessen är isolerad och säker. 

Fördelar med att använda Managed Virtual Network:

- Med en hanterad Virtual Network kan du avlasta arbetsbördan med att hantera Virtual Network till Azure Data Factory. Du behöver inte skapa ett undernät för Azure Integration Runtime som så småningom kan använda många privata IP-adresser från din Virtual Network och kräver en tidigare planering av nätverksinfrastrukturen. 
- Det krävs inte djupgående kunskaper om Azure-nätverk för att göra dataintegreringar på ett säkert sätt. I stället är det mycket enklare för datatekniker att komma igång med säker ETL. 
- Hanterade Virtual Network tillsammans med hanterade privata slutpunkter skyddar mot data exfiltrering. 

> [!IMPORTANT]
>Det hanterade virtuella nätverket stöds för närvarande endast i samma region som Azure Data Factory region.
 

![ADF Managed Virtual Network arkitektur](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Hanterade privata slutpunkter

Hanterade privata slutpunkter är privata slutpunkter som skapas i Azure Data Factory Managed Virtual Network upprättar en privat länk till Azure-resurser. Azure Data Factory hanterar de här privata slutpunkterna för din räkning. 

![Ny hanterad privat slutpunkt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory har stöd för privata länkar. Med Private Link kan du komma åt Azure-tjänster (till exempel Azure Storage, Azure Cosmos DB Azure Synapse Analytics).

När du använder en privat länk passerar trafik mellan dina datalager och Virtual Network helt över Microsofts stamnätverk. Private Link skyddar mot data exfiltreringsrisker. Du upprättar en privat länk till en resurs genom att skapa en privat slutpunkt.

Den privata slutpunkten använder en privat IP-adress i den hanterade Virtual Network för att effektivt föra in tjänsten i den. Privata slutpunkter mappas till en specifik resurs i Azure och inte till hela tjänsten. Kunder kan begränsa anslutningen till en specifik resurs som godkänts av organisationen. Läs mer om [privata länkar och privata slutpunkter.](../private-link/index.yml)

> [!NOTE]
> Vi rekommenderar att du skapar hanterade privata slutpunkter för att ansluta till alla dina Azure-datakällor. 
 
> [!WARNING]
> Om ett PaaS-datalager (Blob, ADLS Gen2, Azure Synapse Analytics) redan har en privat slutpunkt som redan har skapats mot det, och även om det tillåter åtkomst från alla nätverk, kan ADF bara komma åt den med hjälp av hanterad privat slutpunkt. Se till att du skapar en privat slutpunkt i sådana scenarier. 

En privat slutpunktsanslutning skapas i ett väntande tillstånd när du skapar en hanterad privat slutpunkt i Azure Data Factory. Ett arbetsflöde för godkännande initieras. Ägaren till den privata länkresursen ansvarar för att godkänna eller avvisa anslutningen.

![Hantera privat slutpunkt](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Om ägaren godkänner anslutningen upprättas den privata länken. Annars upprättas inte den privata länken. I båda fallen uppdateras den hanterade privata slutpunkten med anslutningens status.

![Godkänn hanterad privat slutpunkt](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Endast en hanterad privat slutpunkt i ett godkänt tillstånd kan skicka trafik till en viss privat länkresurs.

## <a name="interactive-authoring"></a>Interaktiv redigering
Interaktiva redigeringsfunktioner används för funktioner som testanslutning, bläddringsmapplista och tabelllista, hämta schema och förhandsgranskningsdata. Du kan aktivera interaktiv redigering när du skapar eller redigerar en Azure Integration Runtime som finns i ett ADF-hanterat virtuellt nätverk. Servertjänsten allokerar beräkning i förväg för interaktiva redigeringsfunktionaliteter. Annars allokeras beräkningen varje gång en interaktiv åtgärd utförs, vilket tar längre tid. TTL-värdet (Time To Live) för interaktiv redigering är 60 minuter, vilket innebär att det automatiskt inaktiveras efter 60 minuter av den senaste interaktiva redigeringsåtgärden.

![Interaktiv redigering](./media/managed-vnet/interactive-authoring.png)

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Skapa hanterat virtuellt nätverk via Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}"

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
### <a name="supported-data-sources"></a>Datakällor som stöds
Nedanstående datakällor kan ansluta via en privat länk från ADF Managed Virtual Network.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (inkluderar inte Azure SQL Managed Instance)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private Link Service
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure-databas för MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory Managed Virtual Network finns i följande Azure-regioner:
- East US
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- USA, södra centrala
- Central US
- Europa, norra
- Europa, västra
- Storbritannien, södra
- Sydostasien
- Australien, östra
- Australien, sydöstra
- Mellanöstern
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Brasilien, södra
- Frankrike, centrala
- Schweiz, norra
- Storbritannien, västra
- Kanada, östra
- Kanada, centrala

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Utgående kommunikation via offentlig slutpunkt från ADF Managed Virtual Network
- Endast port 443 öppnas för utgående kommunikation.
- Azure Storage och Azure Data Lake-Gen2 stöds inte för anslutning via den offentliga slutpunkten från ett ADF-hanterat virtuellt nätverk.

### <a name="linked-service-creation-of-azure-key-vault"></a>Skapande av en länkad tjänst Azure Key Vault 
- När du skapar en länkad tjänst för Azure Key Vault finns det ingen Azure Integration Runtime-referens. Det gör att du inte kan skapa en privat slutpunkt när en länkad tjänst skapas Azure Key Vault. Men när du skapar en länkad tjänst för datalager som refererar till Azure Key Vault Linked Service och denna länkade tjänst refererar till Azure Integration Runtime med Managed Virtual Network aktiverat, kan du skapa en privat slutpunkt för den länkade Azure Key Vault-tjänsten när den skapas. 
- **Testanslutningsåtgärden** för länkad Azure Key Vault verifierar endast URL-formatet, men gör ingen nätverksåtgärd.
- Kolumnen Använda **privat slutpunkt visas** alltid som tom även om du skapar privat slutpunkt för Azure Key Vault.
![Privat slutpunkt för AKV](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Nästa steg

- Självstudie: [Skapa en kopieringspipeline med hanterade Virtual Network och privata slutpunkter](tutorial-copy-data-portal-private.md) 
- Självstudie: [Skapa en pipeline för dataflödesmappning med hanterade Virtual Network och privata slutpunkter](tutorial-data-flow-private.md)