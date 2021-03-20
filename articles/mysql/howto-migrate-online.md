---
title: Lägsta-nedtid-migrering – Azure Database for MySQL
description: I den här artikeln beskrivs hur du utför en migrering med minimalt avbrott i en MySQL-databas till Azure Database for MySQL med hjälp av Azure Database Migration Service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540339"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal-nedtid-migrering till Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Du kan utföra MySQL-migreringar för att Azure Database for MySQL med minimal nedtid genom att använda den nyligen introducerade **kontinuerliga synkroniseringen** av [Azure Database migration service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar den mängd stillestånds tid som programmet ådragit sig.

Du kan också läsa mer om hur du migrerar databaser till Azure Database for MySQL med hjälp av [Database migration guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) . Den här guiden ger vägledning som leder till en lyckad planering och körning av en MySQL-migrering till Azure.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsning av din lokala plats för Azure Database for MySQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet fortsätter att köras. När data har fångats upp på Azure-sidan, stoppar du programmet under en kort stund (minsta stillestånds tid), väntar på den senaste data insamlingen (från den tidpunkt då du avbryter programmet tills programmet är effektivt otillgängligt för att ta ny trafik) och sedan uppdatera anslutnings strängen så att den pekar på Azure. När du är färdig är ditt program Live på Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Kontinuerlig synkronisering med Azure Database Migration Service":::

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du migrerar databaser till Azure Database for MySQL finns i [Guide för databas migrering](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Visa videon [enkelt migrera MySQL/postgresql-appar till Azure Managed Service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database for MySQL.
- Se självstudien [migrera MySQL till Azure Database for MySQL online med DMS](../dms/tutorial-mysql-azure-mysql-online.md).