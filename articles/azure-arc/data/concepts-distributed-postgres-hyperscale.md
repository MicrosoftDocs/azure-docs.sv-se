---
title: Koncept för att distribuera data och skala ut med ARC Enabled PostgreSQL storskalig Server Group
titleSuffix: Azure Arc enabled data services
description: Koncept för att distribuera data med ARC Enabled PostgreSQL Scale Server Group
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941809"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Koncept för att distribuera data med ARC Enabled PostgreSQL Scale Server Group

I den här artikeln beskrivs viktiga begrepp som är viktiga för att få ut mesta möjliga av Azure Arc-aktiverade PostgreSQL-skalning.
Artiklarna som är länkade nedan pekar på de begrepp som förklaras för Azure Database for PostgreSQL storskalig (citus). Det är samma teknik som Azure Arc Enabled PostgreSQL-storskalig, så samma koncept och perspektiv gäller.

**Vad är skillnaden mellan dem?**
- _Hyperskala (Citus) för Azure Database for PostgreSQL_

Detta är den storskaliga form faktorn för postgres-databasmotorn som är tillgänglig som databas som en tjänst i Azure (PaaS). Den drivs av citus-tillägget som möjliggör skalnings upplevelsen. I det här formuläret körs tjänsten i Microsoft-datacenter och drivs av Microsoft.

- _Azure Arc Enabled PostgreSQL-skalning_

Detta är den storskaliga form faktorn för postgres-databasmotorn som erbjuds tillgänglig med Azure Arc-aktiverad data tjänst. I den här formulärs faktorn tillhandahåller våra kunder den infrastruktur som är värd för systemen och använder dem.

Huvud koncepten kring Azure Arc-aktiverade PostgreSQL-skalning sammanfattas nedan:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Noder och tabeller
Det är viktigt att känna till följande begrepp för att få ut mesta möjliga av Azure Arc-aktiverade postgres-skalning:
- Specialiserade postgres-noder i Azure Arc Enabled PostgreSQL-storskalig: koordinator och arbetare
- Typer av tabeller: distribuerade tabeller, referens tabeller och lokala tabeller
- Shards

Mer information finns på [noder och tabeller i Azure Database for PostgreSQL – storskalig (citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Fastställa program typen
Det är tydligt att identifiera vilken typ av program som du skapar. Varför? Eftersom körningen av effektiva frågor på en Azure-Arc-aktiverad PostgreSQL-Server grupp kräver att tabellerna distribueras på rätt sätt mellan servrar. Den rekommenderade distributionen varierar beroende på typ av program och dess fråge mönster. Det finns många olika typer av program som fungerar bra på Azure Arc-aktiverade postgres-skalning:
- Program med flera klienter
- Realtidsprogram

Det första steget i data modellering är att identifiera vilket av dem som liknar ditt program.

Se information när du [bestämmer program typ](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Välja en distributionskolumn
Varför ska jag välja en distribuerad kolumn?

Detta är ett av de viktigaste modell beslut som du ska fatta. Azure Arc Enabled PostgreSQL-skalning lagrar rader i Shards baserat på värdet för radernas distributions kolumn. Rätt alternativ grupper relaterade data tillsammans på samma fysiska noder, vilket gör att frågor snabbt och lägger till stöd för alla SQL-funktioner. Ett felaktigt val gör att systemet körs långsamt och inte stöder alla SQL-funktioner på noderna. Den här artikeln innehåller tips för distributions kolumner för de två vanligaste storskaliga scenarierna.

Se information i [Välj distributions kolumner](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Tabellsamlokalisering

Samplacering handlar om att lagra relaterad information tillsammans på samma noder. Frågor kan gå snabbt när alla nödvändiga data är tillgängliga utan nätverks trafik. Att placera relaterade data på olika noder gör att frågor kan köras effektivt parallellt på varje nod.

Se information på [tabell-samplacering](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Nästa steg
- [Läs om hur du skapar en Azure Arc-aktiverad PostgreSQL-skalning](create-postgresql-hyperscale-server-group.md)
- [Läs mer om att skala ut Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper som skapats i din ARC-datakontrollant](scale-out-postgresql-hyperscale-server-group.md)
- [Läs om Azure Arc aktiverat Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Läs om Azure-bågen](https://aka.ms/azurearc)

