---
title: Hantera Läs repliker – Azure Portal-Azure Database for MySQL – flexibel Server
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for MySQL flexibel server med hjälp av Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: fd303804706f9ae210e6714cc8698c94c39ebef6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106861"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for MySQL flexibel server med hjälp av Azure Portal

> [!IMPORTANT]
> Läs repliker i Azure Database for MySQL-flexibel Server är i för hands version.

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i den Azure Database for MySQL flexibla servern med hjälp av Azure Portal.

> [!Note]
> Repliken stöds inte på en server med hög tillgänglighet. 

## <a name="prerequisites"></a>Förutsättningar

- En [Azure Database for MySQL server flexibel Server](quickstart-create-server-portal.md) som ska användas som käll Server.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

> [!IMPORTANT]
> När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

Du kan skapa en Läs replik server med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj den befintliga Azure Database for MySQL flexibla server som du vill använda som källa. Den här åtgärden öppnar **översikts** sidan.

3. Välj **replikering** på menyn under **Inställningar**.

4. Välj **Lägg till replik**.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL-replikering":::

5. Ange ett namn på replik servern.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL-replik namn":::

6. Bekräfta skapandet av repliken genom att klicka på **OK** .

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som källan. Replik Server konfigurationen kan ändras efter att den har skapats. Replik servern skapas alltid i samma resurs grupp, samma plats och samma prenumeration som käll servern. Om du vill skapa en replik server till en annan resurs grupp eller en annan prenumeration kan du [Flytta replik servern](../../azure-resource-manager/management/move-resource-group-and-subscription.md) när du har skapat den. Vi rekommenderar att replik serverns konfiguration ska hållas på lika stora eller större värden än källan för att säkerställa att repliken kan fortsätta med källan.

När replik servern har skapats kan den visas från bladet **replikering** .

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Repliker i Azure Database for MySQL lista":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server

> [!IMPORTANT]
> Att stoppa replikeringen till en server går inte att ångra. När replikeringen har stoppats mellan en källa och replik kan den inte återställas. Replik servern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Använd följande steg för att stoppa replikeringen mellan en källa och en replik Server från Azure Portal:

1. I Azure Portal väljer du din käll Azure Database for MySQL flexibel Server. 

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill stoppa replikering för.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL-stoppa replikering Välj server":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. Välj **stoppa replikering**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL-stoppa replikering":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Bekräfta Azure Database for MySQL-stoppa replikering":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Ta bort en replik Server

Gör så här om du vill ta bort en Läs replik Server från Azure Portal:

1. I Azure Portal väljer du din käll Azure Database for MySQL flexibel Server.

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill ta bort.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL-ta bort replikering Välj server":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. Välj **ta bort replik**

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL-ta bort replik":::

5. Skriv namnet på repliken och klicka på **ta bort** för att bekräfta borttagningen av repliken.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Bekräfta Azure Database for MySQL-ta bort replik":::

## <a name="delete-a-source-server"></a>Ta bort en käll Server

> [!IMPORTANT]
> Om du tar bort en källserver stoppas replikeringen till alla replikservrar och själva källservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Gör så här om du vill ta bort en käll Server från Azure Portal:

1. I Azure Portal väljer du din käll Azure Database for MySQL flexibel Server.

2. Välj **ta bort** i **översikten**.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL-ta bort källa":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Skriv namnet på käll servern och klicka på **ta bort** för att bekräfta borttagningen av käll servern.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Bekräfta Azure Database for MySQL-ta bort källa":::

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure Portal](https://portal.azure.com/)väljer du den replik Azure Database for MySQL flexibla server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **mått**:

3. Välj **replikeringsfördröjning i sekunder** från List rutan med tillgängliga mått.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Välj replikeringsfördröjning":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett tidsintervall på 30 minuter.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Välj tidsintervall":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Visa replikeringens fördröjning för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Välj tidsintervall 30 minuter":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Läs repliker](concepts-read-replicas.md)