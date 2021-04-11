---
title: 'Snabb start: skapa en grundläggande nivå Server grupp-storskalig (citus)-Azure Database for PostgreSQL'
description: Kom igång med Basic-nivån Azure Database for PostgreSQL disscale (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024096"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Skapa en citus-Server (Scale-Tier) i gruppen Azure Portal

Azure Database for PostgreSQL-storskalig (citus) är en hanterad tjänst som du använder för att köra, hantera och skala hög tillgängliga PostgreSQL-databaser i molnet. Dess [grundläggande nivå](concepts-hyperscale-tiers.md) är ett användbart distributions alternativ för inledande utveckling och testning.

Den här snabb starten visar hur du skapar en citus-Server grupp (för en storskalig) med hjälp av Azure Portal. Du etablerar Server gruppen och kontrollerar att du kan ansluta till den för att köra frågor.

> [!IMPORTANT]
> Nivån för den storskaliga (citus) Basic-nivån är för närvarande en för hands version.  Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du etablerar en Server grupp för storskaliga (citus). Du är ansluten till den med psql, skapat ett schema och distribuerade data.

- Följ en självstudie för att [bygga skalbara program med flera klienter](./tutorial-design-database-hyperscale-multi-tenant.md)
- Fastställ bästa [initial storlek](howto-hyperscale-scale-initial.md) för Server gruppen
