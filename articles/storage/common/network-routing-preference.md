---
title: Inställningar för nätverks routning
titleSuffix: Azure Storage
description: Med inställningar för nätverks routning kan du ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: bf2270fe6f71dfe5be31db8e82f6c44696f28074
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601499"
---
# <a name="network-routing-preference-for-azure-storage"></a>Inställningar för nätverks routning för Azure Storage

Du kan konfigurera inställningar för nätverks [routning](../../virtual-network/routing-preference-overview.md) för ditt Azure Storage-konto om du vill ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet. Som standard dirigeras trafik från Internet till den offentliga slut punkten för ditt lagrings konto över [Microsofts globala nätverk](../../networking/microsoft-global-network.md). Azure Storage innehåller fler alternativ för att konfigurera hur trafik dirigeras till ditt lagrings konto.

Genom att konfigurera cirkulations inställningen får du flexibiliteten att optimera trafiken antingen för förstklassiga nätverks prestanda eller för kostnad. När du konfigurerar en cirkulations inställning anger du hur trafiken ska dirigeras till den offentliga slut punkten för ditt lagrings konto som standard. Du kan också publicera dirigerade slut punkter för ditt lagrings konto.

> [!NOTE]
> Den här funktionen stöds inte i lagrings konton som har kon figurer ATS för att använda prestanda nivån Premium eller Zone-redundant lagring (ZRS).

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft globalt nätverk jämfört med Internet routning

Som standard har klienter utanför Azure-miljön åtkomst till ditt lagrings konto över Microsofts globala nätverk. Microsofts globala nätverk är optimerat för val av sökväg med låg latens för att leverera förstklassiga nätverks prestanda med hög tillförlitlighet. Både inkommande och utgående trafik dirigeras genom den POP (Point of närvaro) som är närmast klienten. Den här standardroutnings konfigurationen säkerställer att trafik till och från ditt lagrings konto passerar över Microsofts globala nätverk för den stora sökvägen, vilket maximerar nätverkets prestanda.

Du kan ändra konfigurationen för routning för ditt lagrings konto så att både inkommande och utgående trafik dirigeras till och från klienter utanför Azure-miljön via den POP som ligger närmast lagrings kontot. Den här vägen minimerar genom gången av din trafik över Microsofts globala nätverk, som lämnar den till överförings leverantören så snart som möjligt. Användning av den här konfigurationen sänker nätverks kostnaderna.

Följande diagram visar hur trafiken flödar mellan klienten och lagrings kontot för varje flödes inställning:

![Översikt över alternativ för routning för Azure Storage](media/network-routing-preference/routing-options-diagram.png)

Mer information om routnings inställningar i Azure finns i [Vad är cirkulations inställningar?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Konfiguration av Routning

Steg-för-steg-anvisningar som visar hur du konfigurerar inställningar för Routning och dirigerade slut punkter finns i [Konfigurera inställningar för nätverks routning för Azure Storage](configure-network-routing-preference.md).

Du kan välja mellan Microsofts globala nätverk och Internet routning som standardinställning för routning för den offentliga slut punkten för ditt lagrings konto. Inställningen standard routning gäller för all trafik från klienter utanför Azure och påverkar slut punkterna för Azure Data Lake Storage Gen2, Blob Storage, Azure Files och statiska webbplatser. Det går inte att konfigurera cirkulations inställningar för Azure-köer eller Azure-tabeller.

Du kan också publicera dirigerade slut punkter för ditt lagrings konto. När du publicerar cirkulations bara slut punkter skapar Azure Storage nya offentliga slut punkter för ditt lagrings konto som dirigerar trafik över den önskade sökvägen. Den här flexibiliteten gör det möjligt att dirigera trafik till ditt lagrings konto över en viss väg utan att ändra standard inställningarna för routning.

Om du till exempel publicerar en Internet Route-bestämd slut punkt för "StorageAccountA" kommer att publicera följande slut punkter för ditt lagrings konto:

| Lagrings tjänst        | Dirigerad slut punkt                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob Service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Fil tjänst           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statiska webbplatser        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Om du har en Read-Access Geo-redundant lagring (RA-GRS) eller ett lagrings konto med Läs åtkomst för geo-zon-redundant lagring (RA-GZRS) skapas automatiskt de motsvarande slut punkterna i den sekundära regionen för Läs behörighet.

| Lagrings tjänst        | Dirigerad skrivskyddad sekundär slut punkt                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob Service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Fil tjänst           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statiska webbplatser        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Anslutnings strängarna för de publicerade flödes bara slut punkterna kan kopieras via [Azure Portal](https://portal.azure.com). Dessa anslutnings strängar kan användas för auktorisering av delad nyckel med alla befintliga Azure Storage SDK: er och API: er.

## <a name="regional-availability"></a>Regional tillgänglighet

Dirigerings inställningar för Azure Storage är tillgänglig i följande regioner:

- Central US 
- Centrala USA-EUAP
- East US 
- USA, östra 2
- USA, östra 2 
- USA, östra 2 EUAP
- USA, södra centrala
- USA, västra centrala
- USA, västra 
- USA, västra 2 
- Frankrike, centrala 
- Frankrike, södra 
- Tyskland, norra 
- Tyskland, västra centrala 
- USA, norra centrala
- Europa, norra 
- Östra Norge 
- Schweiz, norra
- Schweiz, västra
- Storbritannien, södra 
- Storbritannien, västra 
- Europa, västra 
- Förenade Arabemiraten Central
- Asien, östra 
- Sydostasien 
- Japan, östra 
- Japan, västra 
- Indien, västra
- Australien, östra 
- Australien, sydöstra 

Följande kända problem påverkar Dirigerings inställningen för Azure Storage:

- Åtkomst begär Anden för den dirigerade slut punkten för det globala Microsoft-nätverket fungerar inte med HTTP-fel 404 eller motsvarande. Routning över Microsofts globala nätverk fungerar som förväntat när det har angetts som standard cirkulations inställningar för den offentliga slut punkten.

## <a name="pricing-and-billing"></a>Priser och fakturering

Information om priser och fakturering finns i avsnittet **prissättning** i [Vad är inställningen routning?](../../virtual-network/routing-preference-overview.md#pricing).

## <a name="next-steps"></a>Nästa steg

- [Vad är routningsprioritet?](../../virtual-network/routing-preference-overview.md)
- [Konfigurera inställningar för nätverksroutning](configure-network-routing-preference.md)
- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](storage-network-security.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
