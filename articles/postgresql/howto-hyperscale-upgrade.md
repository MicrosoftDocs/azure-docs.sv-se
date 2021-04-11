---
title: Uppgradera Server grupp – storskalig (citus) – Azure Database for PostgreSQL
description: Den här artikeln beskriver hur du kan uppgradera PostgreSQL och citus i Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024028"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Server grupp för uppgradering av citus-Server

De här anvisningarna beskriver hur du uppgraderar till en ny huvud version av PostgreSQL på alla Server grupps noder.

## <a name="test-the-upgrade-first"></a>Testa uppgraderingen först

Att uppgradera PostgreSQL orsakar fler ändringar än du kanske vill, eftersom citus () även uppgraderar [databas tilläggen](concepts-hyperscale-extensions.md), inklusive citus-tillägget.
Vi rekommenderar starkt att du testar ditt program med den nya PostgreSQL-och citus-versionen innan du uppgraderar din produktions miljö.

Ett bekvämt sätt att testa är att göra en kopia av Server gruppen med hjälp av [återställning](concepts-hyperscale-backup.md#point-in-time-restore-pitr)av tidpunkter. Uppgradera kopian och testa programmet mot det. När du har verifierat att allt fungerar som det ska kan du uppgradera den ursprungliga Server gruppen.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Uppgradera en Server grupp i Azure Portal

1. I **översikts** avsnittet i en disscale-Server (citus)-Server grupp väljer du knappen **Uppgradera** .
1. En dialog ruta visas med den aktuella versionen av PostgreSQL och citus.
   Välj en ny PostgreSQL-version i listan **Uppgradera till** .
1. Kontrol lera att värdet i **citus version efter uppgraderingen** är det du förväntar dig.
   Det här värdet ändras baserat på den PostgreSQL-version som du har valt.
1. Klicka på knappen **Uppgradera** om du vill fortsätta.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [postgresql-versioner som stöds](concepts-hyperscale-versions.md).
* Se [vilka tillägg](concepts-hyperscale-extensions.md) som paketeras med varje postgresql-version i en citus ()-Server grupp.
