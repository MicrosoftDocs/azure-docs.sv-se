---
title: Tillåt åtkomst till indexerings-IP-intervall
titleSuffix: Azure Cognitive Search
description: Konfigurera IP-brandväggens regler för att tillåta data åtkomst av en Azure Kognitiv sökning-indexerare.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757251"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Konfigurera IP-brandvägg för att tillåta index anslutningar (Azure Kognitiv sökning)

IP-brandväggens regler för Azure-resurser, till exempel lagrings konton, Cosmos DB-konton och Azure SQL-servrar tillåter endast trafik från olika IP-adressintervall för åtkomst till data.

Den här artikeln beskriver hur du konfigurerar IP-regler, via Azure Portal, för ett lagrings konto så att Azure Kognitiv sökning indexerare kan komma åt data på ett säkert sätt. Även om det är speciellt för Azure Storage fungerar metoden även för andra Azure-resurser som använder IP-brandvägg för att skydda åtkomsten till data.

> [!NOTE]
> Regler för IP-brandvägg för lagrings kontot är bara effektiva om lagrings kontot och Sök tjänsten finns i olika regioner. Om din installation inte tillåter detta rekommenderar vi att du använder alternativet för [betrott tjänst undantag](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Hämta IP-adressen för Sök tjänsten

Hämta det fullständigt kvalificerade domän namnet (FQDN) för Sök tjänsten. Detta kommer att se ut `<search-service-name>.search.windows.net` . Du kan ta reda på det fullständiga domän namnet genom att leta upp Sök tjänsten på Azure Portal.

   ![Hämta FQDN för tjänst](media\search-indexer-howto-secure-access\search-service-portal.png "Hämta FQDN för tjänst")

IP-adressen för Sök tjänsten kan erhållas genom att utföra en `nslookup` (eller a `ping` ) av FQDN. I exemplet nedan skulle du lägga till "150.0.0.1" i en regel för inkommande trafik i Azure Storage brand väggen. Det kan ta upp till 15 minuter innan brand Väggs inställningarna har uppdaterats för Sök tjänst indexeraren för att få åtkomst till Azure Storage kontot.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Hämta IP-adressintervall för tjänst tag gen "AzureCognitiveSearch"

Ytterligare IP-adresser används för begär Anden som kommer från indexeraren [körnings miljö för flera klienter](search-indexer-securing-resources.md#indexer-execution-environment). Du kan hämta det här IP-adressintervallet från tjänst tag gen.

IP-adressintervall för `AzureCognitiveSearch` service tag gen kan antingen erhållas via [identifierings-API: t (för hands versionen)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) eller den [nedladdnings bara JSON-filen](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Under den här genom gången ska vi anta att Sök tjänsten är det offentliga Azure-molnet. den [offentliga Azure-JSON-filen](https://www.microsoft.com/download/details.aspx?id=56519) bör laddas ned.

   ![Ladda ned JSON-fil](media\search-indexer-howto-secure-access\service-tag.png "Ladda ned JSON-fil")

Från JSON-filen, förutsatt att Sök tjänsten är i västra centrala USA, visas listan över IP-adresser för körnings miljön för flera klient organisationer i listan nedan.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

För/32 IP-adresser tar du bort "/32" (52.253.133.74/32-> 52.253.133.74), andra kan användas orda Grant.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Lägg till IP-adressintervall i brand Väggs regler

Det enklaste sättet att lägga till IP-adressintervall till ett lagrings kontos brand Väggs regel är via Azure Portal. Leta upp lagrings kontot på portalen och gå till fliken **brand väggar och virtuella nätverk** .

   ![Brand vägg och virtuella nätverk](media\search-indexer-howto-secure-access\storage-firewall.png "Brand vägg och virtuella nätverk")

Lägg till de tre IP-adresserna som hämtades tidigare (1 för Sök tjänstens IP-adress, 2 för `AzureCognitiveSearch` tjänst tag gen) i adress intervallet och välj **Spara**.

   ![IP-regler för brand vägg](media\search-indexer-howto-secure-access\storage-firewall-ip.png "IP-regler för brand vägg")

Brand Väggs reglerna tar 5-10 minuter att uppdateras, och sedan kan indexerare få åtkomst till data i lagrings kontot.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure Storage brand väggar](../storage/common/storage-network-security.md)
- [Konfigurera IP-brandvägg för Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Konfigurera IP-brandvägg för Azure SQL Server](../azure-sql/database/firewall-configure.md)