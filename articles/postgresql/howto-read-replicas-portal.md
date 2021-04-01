---
title: Hantera Läs repliker – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du hanterar Läs repliker Azure Database for PostgreSQL-enskild server från Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422186"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for PostgreSQL-enskild server från Azure Portal

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for PostgreSQL från Azure Portal. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).


## <a name="prerequisites"></a>Förutsättningar
En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md) som ska vara den primära servern.

## <a name="azure-replication-support"></a>Stöd för Azure-replikering

[Läsning av repliker](concepts-read-replicas.md) och [logisk avkodning](concepts-logical.md) är beroende av postgres Write Ahead-loggen (Wal) för information. De här två funktionerna behöver olika loggnings nivåer från postgres. Logisk avkodning kräver en högre loggnings nivå än Läs repliker.

Om du vill konfigurera rätt loggnings nivå använder du parametern Azure Replication support. Support för Azure-replikering har tre inställnings alternativ:

* **Off** – lägger till minst information i Wal. Den här inställningen är inte tillgänglig på de flesta Azure Database for PostgreSQL-servrar.  
* **Replik** – mer utförligt än **.** Detta är den lägsta loggnings nivå som krävs för att [läsa repliker](concepts-read-replicas.md) ska fungera. Den här inställningen är standard på de flesta servrar.
* **Logisk** – mer utförlig än **replik**. Detta är den lägsta loggnings nivån för logisk avkodning att arbeta. Läs repliker fungerar också med den här inställningen.


> [!NOTE]
> När du distribuerar Läs repliker för permanenta tunga Skriv intensiva primär arbets belastningar kan replikeringen fortsätta att växa och kanske aldrig kan fånga upp med den primära. Detta kan också öka lagrings användningen på den primära servern eftersom WAL-filerna inte tas bort förrän de tas emot på repliken.

## <a name="prepare-the-primary-server"></a>Förbered den primära servern

1. I Azure Portal väljer du en befintlig Azure Database for PostgreSQL server som ska användas som huvud server.

2. Från menyn på servern väljer du **replikering**. Om support för Azure Replication har angetts till minst **replik** kan du skapa Läs repliker. 

3. Om stöd för Azure Replication inte är inställt på minst **replik** anger du det. Välj **Spara**.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL-replikering-Ställ in replik och spara":::

4. Starta om servern för att tillämpa ändringen genom att välja **Ja**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL-replikering-bekräfta omstart":::

5. Du får två Azure Portal-meddelanden när åtgärden har slutförts. Det finns ett meddelande om att uppdatera Server parametern. Det finns ett annat meddelande för omstart av servern som följer omedelbart.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Lyckade aviseringar":::

6. Uppdatera den Azure Portal sidan om du vill uppdatera verktygsfältet replikering. Nu kan du skapa Läs repliker för den här servern.
   

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik
Följ dessa steg om du vill skapa en Läs replik:

1. Välj en befintlig Azure Database for PostgreSQL server som ska användas som primär server. 

2. Välj **replikering** under **Inställningar** på Server panelen.

3. Välj **Lägg till replik**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Lägg till en replik":::

4. Ange ett namn på Läs repliken. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Namnge repliken":::

5. Välj en plats för repliken. Standard platsen är samma som den primära serverns.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Välja en plats":::

   > [!NOTE]
   > Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

6. Bekräfta skapandet av repliken genom att klicka på **OK** .

När Läs repliken har skapats kan den visas från fönstret **replikering** :

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Visa den nya repliken i fönstret replikering":::
 

> [!IMPORTANT]
> Läs igenom [avsnittet överväganden i Översikt över läsning av replik](concepts-read-replicas.md#considerations).
>
> Innan en primär server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikeringen mellan en primär server och en Läs replik.

> [!IMPORTANT]
> När du har stoppat replikeringen till en primär server och en Läs replik kan du inte återställa den. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

Följ dessa steg om du vill stoppa replikeringen mellan en primär server och en Läs replik från Azure Portal:

1. I Azure Portal väljer du den primära Azure Database for PostgreSQL servern.

2. På Server-menyn under **Inställningar** väljer du **replikering**.

3. Välj den replik Server för vilken du vill stoppa replikeringen.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Välj repliken":::
 
4. Välj **stoppa replikering**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Välj stoppa replikering":::
 
5. Välj **OK** för att stoppa replikeringen.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Bekräfta att replikeringen ska stoppas":::
 

## <a name="delete-a-primary-server"></a>Ta bort en primär server
Om du vill ta bort en primär server använder du samma steg som för att ta bort en fristående Azure Database for PostgreSQL Server. 

> [!IMPORTANT]
> När du tar bort en primär server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

Följ dessa steg om du vill ta bort en server från Azure Portal:

1. I Azure Portal väljer du den primära Azure Database for PostgreSQL servern.

2. Öppna sidan **Översikt** för servern. Välj **Ta bort**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="På sidan Server översikt väljer du att ta bort den primära servern":::
 
3. Ange namnet på den primära servern som ska tas bort. Bekräfta borttagning av den primära servern genom att välja **ta bort** .

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Bekräfta att du vill ta bort den primära servern":::
 

## <a name="delete-a-replica"></a>Ta bort en replik
Du kan ta bort en Läs replik som liknar hur du tar bort en primär server.

- I Azure Portal öppnar du sidan **Översikt** för Läs repliken. Välj **Ta bort**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="På sidan replik översikt väljer du om du vill ta bort repliken":::
 
Du kan också ta bort Läs repliken från fönstret **replikering** genom att följa dessa steg:

1. I Azure Portal väljer du den primära Azure Database for PostgreSQL servern.

2. På Server-menyn under **Inställningar** väljer du **replikering**.

3. Välj den Läs replik som ska tas bort.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Välj den replik som ska tas bort":::
 
4. Välj **ta bort replik**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Välj Ta bort replik":::
 
5. Ange namnet på repliken som ska tas bort. Bekräfta borttagning av repliken genom att välja **ta bort** .

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Bekräfta borttagning av te-replik":::
 

## <a name="monitor-a-replica"></a>Övervaka en replik
Det finns två mått för att övervaka Läs repliker.

### <a name="max-lag-across-replicas-metric"></a>Mått för högsta fördröjning i repliker
Måttet **Max fördröjning över repliker** visar fördröjningen i byte mellan den primära servern och den mest isolerings repliken. 

1.  I Azure Portal väljer du den primära Azure Database for PostgreSQL servern.

2.  Välj **Mått**. I fönstret **mått** väljer du **Max fördröjning över repliker**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Övervaka maximal fördröjning mellan repliker":::
 
3.  För din **agg regering** väljer du **Max**.


### <a name="replica-lag-metric"></a>Mått för replik fördröjning
Värdet för **replik fördröjningen** visar tiden sedan den senaste återspelade transaktionen på en replik. Om det inte finns några transaktioner i din Master, återspeglar måttet denna tids fördröjning.

1. I Azure Portal väljer du Azure Database for PostgreSQL Läs replik.

2. Välj **Mått**. I fönstret **mått** väljer du **replik fördröjning**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Övervaka replik fördröjningen":::
 
3. För din **agg regering** väljer du **Max**. 
 
## <a name="next-steps"></a>Nästa steg
* Läs mer om att [läsa repliker i Azure Database for PostgreSQL](concepts-read-replicas.md).
* Lär dig hur du [skapar och hanterar Läs repliker i Azure CLI och REST API](howto-read-replicas-cli.md).