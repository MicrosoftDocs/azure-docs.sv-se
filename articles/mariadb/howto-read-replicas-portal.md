---
title: Hantera Läs repliker – Azure Portal-Azure Database for MariaDB
description: Den här artikeln beskriver hur du konfigurerar och hanterar Läs repliker i Azure Database for MariaDB med hjälp av portalen
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: fa2afdccf9c8bd6ec7f5808674e6eec968838d10
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665012"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for MariaDB med hjälp av Azure Portal

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MariaDB-tjänsten med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-portal.md) som ska användas som käll Server.

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MariaDB servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att käll servern är i någon av dessa pris nivåer.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

> [!IMPORTANT]
> När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

Du kan skapa en Läs replik server med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj den befintliga Azure Database for MariaDB-server som du vill använda som huvud server. Den här åtgärden öppnar **översikts** sidan.

3. Välj **replikering** på menyn under **Inställningar**.

4. Välj **Lägg till replik**.

   ![Azure Database for MariaDB-replikering](./media/howto-read-replica-portal/add-replica.png)

5. Ange ett namn på replik servern.

    ![Azure Database for MariaDB-replik namn](./media/howto-read-replica-portal/replica-name.png)

6. Välj plats för replik servern. Standard platsen är samma som käll servern.

    ![Azure Database for MariaDB-replik plats](./media/howto-read-replica-portal/replica-location.png)

7. Bekräfta skapandet av repliken genom att klicka på **OK** .

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som huvud servern. Replik Server konfigurationen kan ändras efter att den har skapats. Vi rekommenderar att replik serverns konfiguration måste vara lika med eller större värden än källan för att säkerställa att repliken kan hållas kvar med huvud servern.

När replik servern har skapats kan den visas från bladet **replikering** .

   ![Repliker i Azure Database for MariaDB lista](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server

> [!IMPORTANT]
> Att stoppa replikeringen till en server går inte att ångra. När replikeringen har stoppats mellan en källa och replik kan den inte återställas. Replik servern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Använd följande steg för att stoppa replikeringen mellan en källa och en replik Server från Azure Portal:

1. I Azure Portal väljer du käll Azure Database for MariaDB Server. 

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill stoppa replikering för.

   ![Azure Database for MariaDB-stoppa replikering Välj server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Välj **stoppa replikering**.

   ![Azure Database for MariaDB-stoppa replikering](./media/howto-read-replica-portal/stop-replication.png)

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   ![Bekräfta Azure Database for MariaDB-stoppa replikering](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Ta bort en replik Server

Gör så här om du vill ta bort en Läs replik Server från Azure Portal:

1. I Azure Portal väljer du käll Azure Database for MariaDB Server.

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill ta bort.

   ![Azure Database for MariaDB-ta bort replikering Välj server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Välj **ta bort replik**

   ![Azure Database for MariaDB-ta bort replik](./media/howto-read-replica-portal/delete-replica.png)

5. Skriv namnet på repliken och klicka på **ta bort** för att bekräfta borttagningen av repliken.  

   ![Bekräfta Azure Database for MariaDB-ta bort replik](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Ta bort en käll Server

> [!IMPORTANT]
> Om du tar bort en källserver stoppas replikeringen till alla replikservrar och själva källservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Gör så här om du vill ta bort en käll Server från Azure Portal:

1. I Azure Portal väljer du käll Azure Database for MariaDB Server.

2. Välj **ta bort** i **översikten**.

   ![Azure Database for MariaDB-ta bort huvud](./media/howto-read-replica-portal/delete-master-overview.png)

3. Skriv namnet på käll servern och klicka på **ta bort** för att bekräfta borttagningen av käll servern.  

   ![Bekräfta Azure Database for MariaDB-ta bort original](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure Portal](https://portal.azure.com/)väljer du den replik Azure Database for MariaDBs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **mått**:

3. Välj **replikeringsfördröjning i sekunder** från List rutan med tillgängliga mått.

   ![Välj replikeringsfördröjning](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett tidsintervall på 30 minuter.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visa replikeringens fördröjning för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna för en stor arbets belastning.

   ![Välj tidsintervall 30 minuter](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Läs repliker](concepts-read-replicas.md)
