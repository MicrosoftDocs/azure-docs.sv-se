---
title: Hantera Läs repliker – Azure Portal-Azure Database for MySQL
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for MySQL att använda Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 26b503e7d55ed3d2f9bd06837551655e7af05a17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541948"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for MySQL med hjälp av Azure Portal

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MySQL-tjänsten med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md) som ska användas som käll Server.

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MySQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att käll servern är i någon av dessa pris nivåer.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

> [!IMPORTANT]
> När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

Du kan skapa en Läs replik server med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj den befintliga Azure Database for MySQL-server som du vill använda som huvud server. Den här åtgärden öppnar **översikts** sidan.

3. Välj **replikering** på menyn under **Inställningar**.

4. Välj **Lägg till replik**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL-replikering":::

5. Ange ett namn på replik servern.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL-replik namn":::

6. Välj plats för replik servern. Standard platsen är samma som käll servern.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL-replik plats":::

   > [!NOTE]
   > Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

7. Bekräfta skapandet av repliken genom att klicka på **OK** .

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som huvud servern. Replik Server konfigurationen kan ändras efter att den har skapats. Replik servern skapas alltid i samma resurs grupp och samma prenumeration som käll servern. Om du vill skapa en replik server till en annan resurs grupp eller en annan prenumeration kan du [Flytta replik servern](../azure-resource-manager/management/move-resource-group-and-subscription.md) när du har skapat den. Vi rekommenderar att replik serverns konfiguration måste vara lika med eller större värden än källan för att säkerställa att repliken kan hållas kvar med huvud servern.

När replik servern har skapats kan den visas från bladet **replikering** .

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Repliker i Azure Database for MySQL lista":::

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server

> [!IMPORTANT]
> Att stoppa replikeringen till en server går inte att ångra. När replikeringen har stoppats mellan en källa och replik kan den inte återställas. Replik servern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Använd följande steg för att stoppa replikeringen mellan en källa och en replik Server från Azure Portal:

1. I Azure Portal väljer du käll Azure Database for MySQL server. 

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill stoppa replikering för.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL-stoppa replikering Välj server":::

4. Välj **stoppa replikering**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL-stoppa replikering":::

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Bekräfta Azure Database for MySQL-stoppa replikering":::

## <a name="delete-a-replica-server"></a>Ta bort en replik Server

Gör så här om du vill ta bort en Läs replik Server från Azure Portal:

1. I Azure Portal väljer du käll Azure Database for MySQL server.

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill ta bort.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL-ta bort replikering Välj server":::

4. Välj **ta bort replik**

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL-ta bort replik":::

5. Skriv namnet på repliken och klicka på **ta bort** för att bekräfta borttagningen av repliken.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Bekräfta Azure Database for MySQL-ta bort replik":::

## <a name="delete-a-source-server"></a>Ta bort en käll Server

> [!IMPORTANT]
> Om du tar bort en källserver stoppas replikeringen till alla replikservrar och själva källservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Gör så här om du vill ta bort en käll Server från Azure Portal:

1. I Azure Portal väljer du käll Azure Database for MySQL server.

2. Välj **ta bort** i **översikten**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL-ta bort huvud":::

3. Skriv namnet på käll servern och klicka på **ta bort** för att bekräfta borttagningen av käll servern.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Bekräfta Azure Database for MySQL-ta bort original":::

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure Portal](https://portal.azure.com/)väljer du den replik Azure Database for MySQLs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **mått**:

3. Välj **replikeringsfördröjning i sekunder** från List rutan med tillgängliga mått.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Välj replikeringsfördröjning":::

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett tidsintervall på 30 minuter.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Välj tidsintervall":::

5. Visa replikeringens fördröjning för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Välj tidsintervall 30 minuter":::

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Läs repliker](concepts-read-replicas.md)