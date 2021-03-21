---
title: Loggar – storskalig (citus) – Azure Database for PostgreSQL
description: Så här kommer du åt databas loggar för Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577071"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Loggar i Azure Database for PostgreSQL-storskalig (citus)

PostgreSQL-loggar finns på varje nod i en citus-servergrupp (). Du kan leverera loggar till en lagrings Server eller till en Analytics-tjänst. Loggarna kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda.

## <a name="accessing-logs"></a>Komma åt loggar

Om du vill komma åt PostgreSQL-loggar för en citus-koordinator eller arbetsnod öppnar du noden i Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lista över noder":::

Öppna **diagnostikinställningar** på den markerade noden och klicka på **+ Lägg till diagnostisk inställning**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Knappen Lägg till diagnostikinställningar":::

Välj ett namn för de nya diagnostikinställningar och markera rutan **PostgreSQLLogs** .  Välj vilka mål som ska ta emot loggarna.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Välj PostgreSQL-loggar":::

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Log Analytics-frågor](../azure-monitor/logs/log-analytics-tutorial.md)
- Lär dig mer om [Azure Event Hub](../event-hubs/event-hubs-about.md)