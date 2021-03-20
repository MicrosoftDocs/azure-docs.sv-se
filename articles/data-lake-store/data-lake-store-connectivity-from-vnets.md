---
title: Ansluta till Azure Data Lake Storage Gen1 från virtuella nätverk | Microsoft Docs
description: Lär dig hur du aktiverar åtkomst till Azure Data Lake Storage Gen1 från virtuella Azure-datorer som har begränsad åtkomst till resurser.
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: e319cf9dfc01546607e20572c5bf4930fd974c75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104044"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Åtkomst Azure Data Lake Storage Gen1 från virtuella datorer i ett Azure VNET
Azure Data Lake Storage Gen1 är en PaaS-tjänst som körs på offentliga Internet-IP-adresser. Alla servrar som kan ansluta till det offentliga Internet kan vanligt vis ansluta till Azure Data Lake Storage Gen1 slut punkter. Som standard har alla virtuella datorer som finns i Azure virtuella nätverk åtkomst till Internet och kan därför komma åt Azure Data Lake Storage Gen1. Det är dock möjligt att konfigurera virtuella datorer i ett virtuellt nätverk till att inte ha åtkomst till Internet. För sådana virtuella datorer är även åtkomst till Azure Data Lake Storage Gen1 begränsad. Det går att blockera offentlig Internet åtkomst för virtuella datorer i Azure virtuella nätverk med någon av följande metoder:

* Genom att konfigurera nätverks säkerhets grupper (NSG)
* Genom att konfigurera användardefinierade vägar (UDR)
* Genom att utväxla vägar via BGP (branschstandardiserade protokoll för dynamisk routning) när ExpressRoute används, blockeras åtkomsten till Internet

I den här artikeln får du lära dig hur du aktiverar åtkomst till Azure Data Lake Storage Gen1 från virtuella Azure-datorer, som har begränsad åtkomst till resurser med någon av de tre metoderna som anges ovan.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Aktivera anslutning till Azure Data Lake Storage Gen1 från virtuella datorer med begränsad anslutning
För att få åtkomst till Azure Data Lake Storage Gen1 från sådana virtuella datorer måste du konfigurera dem för att få åtkomst till IP-adressen för den region där Azure Data Lake Storage Gen1 kontot är tillgängligt. Du kan identifiera IP-adresserna för dina Data Lake Storage Gen1 konto områden genom att matcha DNS-namnen för dina konton ( `<account>.azuredatalakestore.net` ). Du kan använda verktyg som **nslookup** för att lösa DNS-namn för dina konton. Öppna en kommando tolk på datorn och kör följande kommando:

```console
nslookup mydatastore.azuredatalakestore.net
```

Resultatet ser ut ungefär så här. Egenskapen värde mot **adress** är den IP-adress som är kopplad till ditt data Lake Storage gen1-konto.

```output
Non-authoritative answer:
Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
Address:  104.44.88.112
Aliases:  mydatastore.azuredatalakestore.net
```


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Aktivera anslutning från virtuella datorer som är begränsade med NSG
När en NSG-regel används för att blockera åtkomst till Internet, kan du skapa en annan NSG som ger åtkomst till Data Lake Storage Gen1 IP-adress. Mer information om NSG-regler finns i [Översikt över nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md). Instruktioner för hur du skapar NSG: er finns i [så här skapar du en nätverks säkerhets grupp](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Aktivera anslutning från virtuella datorer som är begränsade med UDR eller ExpressRoute
När vägar, antingen UDR eller BGP-utbytta vägar, används för att blockera åtkomst till Internet, måste en särskild väg konfigureras så att virtuella datorer i dessa undernät kan komma åt Data Lake Storage Gen1 slut punkter. Mer information finns i [Översikt över användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md). Instruktioner om hur du skapar UDR finns i [skapa UDR i Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Aktivera anslutning från virtuella datorer som är begränsade med ExpressRoute
När en ExpressRoute-krets konfigureras kan de lokala servrarna komma åt Data Lake Storage Gen1 via offentlig peering. Mer information om hur du konfigurerar ExpressRoute för offentlig peering finns i [vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Skydda data som lagras i Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)