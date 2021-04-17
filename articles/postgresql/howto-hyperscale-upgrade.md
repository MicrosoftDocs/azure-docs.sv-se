---
title: Uppgradera servergrupp – Hyperskala (Citus) – Azure Database for PostgreSQL
description: Den här artikeln beskriver hur du kan uppgradera PostgreSQL och Citus i Azure Database for PostgreSQL – Hyperskala (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518906"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Uppgradera Hyperskala (Citus) servergrupp

De här anvisningarna beskriver hur du uppgraderar till en ny huvudversion av PostgreSQL på alla servergruppnoder.

## <a name="test-the-upgrade-first"></a>Testa uppgraderingen först

Uppgradering av PostgreSQL medför fler ändringar än du kan föreställa dig, eftersom Hyperskala (Citus) även uppgraderar databastilläggen [,](concepts-hyperscale-extensions.md)inklusive Citus-tillägget.
Vi rekommenderar starkt att du testar ditt program med den nya PostgreSQL- och Citus-versionen innan du uppgraderar produktionsmiljön.

Ett enkelt sätt att testa är att göra en kopia av servergruppen med hjälp av återställning [till tidpunkt.](concepts-hyperscale-backup.md#restore) Uppgradera kopian och testa programmet mot det. När du har kontrollerat att allt fungerar som det ska uppgraderar du den ursprungliga servergruppen.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Uppgradera en servergrupp i Azure Portal

1. I avsnittet **Översikt** i en Hyperskala (Citus) servergrupp väljer du **knappen** Uppgradera.
1. En dialogruta visas som visar den aktuella versionen av PostgreSQL och Citus.
   Välj en ny PostgreSQL-version i **listan Uppgradera till.**
1. Kontrollera att värdet i **Citus-versionen efter uppgraderingen är** det du förväntar dig.
   Det här värdet ändras baserat på den PostgreSQL-version som du har valt.
1. Välj knappen **Uppgradera** för att fortsätta.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [PostgreSQL-versioner som stöds.](concepts-hyperscale-versions.md)
* Se [vilka tillägg](concepts-hyperscale-extensions.md) som paketeras med varje PostgreSQL-version i en Hyperskala (Citus)-servergrupp.
