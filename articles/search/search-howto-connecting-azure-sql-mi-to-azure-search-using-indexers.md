---
title: Azure SQL-hanterad instans anslutning för Sök indexering
titleSuffix: Azure Cognitive Search
description: Aktivera offentlig slut punkt för att tillåta anslutningar till SQL-hanterade instanser från en indexerare på Azure Kognitiv sökning.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294262"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Konfigurera en anslutning från en Azure Kognitiv sökning-indexerare till SQL-hanterad instans

Som anges i [ansluta Azure SQL Database till Azure kognitiv sökning med hjälp av indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), stöds att skapa indexerare mot **SQL-hanterade instanser** av Azure kognitiv sökning via den offentliga slut punkten.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Skapa en Azure SQL-hanterad instans med offentlig slut punkt
Skapa en SQL-hanterad instans med alternativet **Aktivera offentlig slut punkt** valt.

   ![Aktivera offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Aktivera offentlig slut punkt")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Aktivera offentlig slut punkt för Azure SQL Managed instance
Du kan också aktivera offentlig slut punkt på en befintlig SQL-hanterad instans under   >    >  **offentlig slut punkt** för säkerhet virtuellt nätverk  >  .

   ![Aktivera offentlig slut punkt med hanterad instans VNET](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Aktivera offentlig slut punkt")

## <a name="verify-nsg-rules"></a>Verifiera NSG-regler
Kontrol lera att nätverks säkerhets gruppen har rätt **inkommande säkerhets regler** som tillåter anslutningar från Azure-tjänster.

   ![NSG inkommande säkerhets regel](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG inkommande säkerhets regel")

> [!NOTE]
> Indexerare kräver fortfarande att SQL-hanterad instans konfigureras med en offentlig slut punkt för att läsa data.
> Du kan dock välja att begränsa den inkommande åtkomsten till den offentliga slut punkten genom att ersätta den aktuella regeln ( `public_endpoint_inbound` ) med följande 2 regler:
>
> * Tillåter inkommande åtkomst från `AzureCognitiveSearch` [tjänst tag gen](../virtual-network/service-tags-overview.md#available-service-tags) ("källa" = `AzureCognitiveSearch` , "namn" = `cognitive_search_inbound` )
>
> * Tillåter inkommande åtkomst från IP-adressen för Sök tjänsten, som kan hämtas genom att pinga det fullständigt kvalificerade domän namnet (t. ex. `<your-search-service-name>.search.windows.net` ). ("källa" = `IP address` , "namn" = `search_service_inbound` )
>
> För var och en av dessa 2 regler anger du "PORT" = `3342` , "protokoll" = `TCP` , "mål" = `Any` , "åtgärd" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Hämta anslutnings sträng för offentlig slut punkt
Se till att du använder anslutnings strängen för den **offentliga slut punkten** (port 3342, inte port 1433).

   ![Anslutnings sträng för offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Anslutnings sträng för offentlig slut punkt")

## <a name="next-steps"></a>Nästa steg
Med konfigurationen av kan du nu ange en SQL-hanterad instans som data källa för en Azure Kognitiv sökning-indexerare med hjälp av antingen portalen eller REST API. Mer information finns i [ansluta Azure SQL Database till Azure kognitiv sökning med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .