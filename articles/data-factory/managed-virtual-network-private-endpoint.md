---
title: Hanterade virtuella nätverk & hanterade privata slut punkter
description: Lär dig mer om hanterade virtuella nätverk och hanterade privata slut punkter i Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 6730a5cfca1b6c04d3c738aac2bdf3c097d5daf5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303210"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory hanterad Virtual Network (förhands granskning)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln förklarar hanterade Virtual Network och hanterade privata slut punkter i Azure Data Factory.


## <a name="managed-virtual-network"></a>Hanterat virtuellt nätverk

När du skapar en Azure Integration Runtime (IR) i Azure Data Factory hanterade Virtual Network (VNET), tillhandahålls integrerings körningen med den hanterade Virtual Network och använder privata slut punkter för att på ett säkert sätt ansluta till data lager som stöds. 

Genom att skapa en Azure IR inom hanterade Virtual Network ser du till att data integrerings processen är isolerad och säker. 

Fördelar med att använda hanterade Virtual Network:

- Med en hanterad Virtual Network kan du avlasta hanteringen av Virtual Network till Azure Data Factory. Du behöver inte skapa ett undernät för Azure Integration Runtime som skulle kunna använda många privata IP-adresser från din Virtual Network och kräver en tidigare infrastruktur för nätverks infrastruktur. 
- Det krävs ingen djup Azure-nätverks kunskap för att utföra data integrering på ett säkert sätt. I stället för att komma igång med säker ETL är det mycket enklare för data tekniker. 
- Hanterade Virtual Network tillsammans med hanterade privata slut punkter skyddar mot data exfiltrering. 

> [!IMPORTANT]
>För närvarande stöds endast hanterade VNet i samma region som Azure Data Factory region.
 

![ADF Managed Virtual Network-arkitektur](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Hanterade privata slutpunkter

Hanterade privata slut punkter är privata slut punkter som skapats i den Azure Data Factory hanterade Virtual Network att upprätta en privat länk till Azure-resurser. Azure Data Factory hanterar de här privata slutpunkterna för din räkning. 

![Ny hanterad privat slut punkt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory stöder privata länkar. Med privat länk kan du komma åt Azure-tjänster (PaaS) (till exempel Azure Storage, Azure Cosmos DB Azure Synapse Analytics).

När du använder en privat länk passerar trafiken mellan dina data lager och hanterade Virtual Network helt över Microsoft stamnät nätverket. Privat länk skyddar mot data exfiltrering-risker. Du upprättar en privat länk till en resurs genom att skapa en privat slut punkt.

Privat slut punkt använder en privat IP-adress i den hanterade Virtual Network för att effektivt flytta tjänsten till den. Privata slut punkter mappas till en angiven resurs i Azure och inte till hela tjänsten. Kunder kan begränsa anslutningen till en speciell resurs som godkänts av organisationen. Lär dig mer om [privata länkar och privata slut punkter](../private-link/index.yml).

> [!NOTE]
> Vi rekommenderar att du skapar hanterade privata slut punkter för att ansluta till alla dina Azure-datakällor. 
 
> [!WARNING]
> Om ett PaaS data lager (BLOB, ADLS Gen2, Azure Synapse Analytics) har skapat en privat slut punkt som redan har skapats mot den, och även om den tillåter åtkomst från alla nätverk, skulle ADF bara kunna komma åt den med hanterad privat slut punkt. Se till att du skapar en privat slut punkt i sådana scenarier. 

En privat slut punkts anslutning skapas i ett väntande tillstånd när du skapar en hanterad privat slut punkt i Azure Data Factory. Ett arbets flöde för godkännande har initierats. Ägaren till den privata länk resursen ansvarar för att godkänna eller avvisa anslutningen.

![Hantera privat slut punkt](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Om ägaren godkänner anslutningen upprättas den privata länken. Annars upprättas inte den privata länken. I båda fallen kommer den hanterade privata slut punkten att uppdateras med anslutningens status.

![Godkänn hanterad privat slut punkt](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Endast en hanterad privat slutpunkt i ett godkänt tillstånd kan skicka trafik till en specifik privat länk resurs.

## <a name="interactive-authoring"></a>Interaktiv redigering
Interaktiva redigerings funktioner används för funktioner som test anslutning, bläddra i Mapplista och tabell lista, Hämta schema och förhandsgranska data. Du kan aktivera interaktiv redigering när du skapar eller redigerar en Azure Integration Runtime som finns i ADF-hanterade virtuella nätverk. Backend-tjänsten kommer att allokera beräkning för interaktiva redigerings funktioner. Annars allokeras beräkningen varje gång en interaktiv åtgärd utförs som tar längre tid. TTL-värdet (Time to Live) för interaktiv redigering är 60 minuter, vilket innebär att det kommer att inaktive ras automatiskt efter 60 minuter efter den senaste interaktiva redigerings åtgärden.

![Interaktiv redigering](./media/managed-vnet/interactive-authoring.png)

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
### <a name="supported-data-sources"></a>Datakällor som stöds
Under data källor finns stöd för att ansluta via en privat länk från ADF-hanterade Virtual Network.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (inte inklusive Azure SQL-hanterad instans)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure privat länk-tjänst
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
- Östra Norge
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Brasilien, södra
- Frankrike, centrala
- Schweiz, norra
- Storbritannien, västra
- Kanada, östra
- Kanada, centrala

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Utgående kommunikation via offentlig slut punkt från ADF-hanterad Virtual Network
- Endast port 443 är öppen för utgående kommunikation.
- Azure Storage och Azure Data Lake-Gen2 stöds inte för anslutning via den offentliga slutpunkten från ett ADF-hanterat virtuellt nätverk.

### <a name="linked-service-creation-of-azure-key-vault"></a>Azure Key Vault för att skapa länkade tjänster 
- När du skapar en länkad tjänst för Azure Key Vault finns det ingen Azure Integration Runtime-referens. Det går inte att skapa en privat slut punkt under skapandet av den länkade tjänsten Azure Key Vault. Men när du skapar en länkad tjänst för data lager som refererar Azure Key Vault länkade tjänsten och den här länkade tjänsten refererar Azure Integration Runtime med hanterade Virtual Network aktiverade, kan du skapa en privat slut punkt för den Azure Key Vault länkade tjänsten under skapandet. 
- **Test anslutnings** åtgärd för länkad tjänst för Azure Key Vault verifierar bara URL-formatet, men ingen nätverks åtgärd.
- Kolumnen som **använder privat slut punkt** visas alltid som Tom även om du skapar en privat slut punkt för Azure Key Vault.
![Privat slut punkt för AKV](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Nästa steg

- Självstudie: [bygga en kopia av pipelinen med hanterade Virtual Network och privata slut punkter](tutorial-copy-data-portal-private.md) 
- Självstudie: [bygga avbildning av data flödes pipeline med hanterade Virtual Network och privata slut punkter](tutorial-data-flow-private.md)