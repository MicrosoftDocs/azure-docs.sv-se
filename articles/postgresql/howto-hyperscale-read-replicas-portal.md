---
title: Hantera Läs repliker – Azure Portal-Azure Database for PostgreSQL-Scale (citus)
description: Lär dig hur du hanterar Läs repliker Azure Database for PostgreSQL-storskalig (citus) från Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024038"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for PostgreSQL-storskalig (citus) från Azure Portal

> [!IMPORTANT]
> Läs repliker i storskaliga (citus) är för närvarande en för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i storskaliga (citus) från Azure Portal. Mer information om Läs repliker finns i [översikten](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Förutsättningar

En [storskalig (citus)-Server grupp](quickstart-create-hyperscale-portal.md) som ska vara primär.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Följ dessa steg om du vill skapa en Läs replik:

1. Välj en befintlig Azure Database for PostgreSQL Server grupp som ska användas som primär. 

2. Välj **replikering** under **Server grupp hantering** på sid panelen för Server gruppen.

3. Välj **Lägg till replik**.

4. Ange ett namn på Läs repliken. 

5. Bekräfta skapandet av repliken genom att klicka på **OK** .

När du har skapat Läs repliken kan du se den från fönstret **replikering** .

> [!IMPORTANT]
>
> Läs igenom [avsnittet överväganden i Översikt över läsning av replik](concepts-hyperscale-read-replicas.md#considerations).
>
> Innan en primär server grupps inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

## <a name="delete-a-primary-server-group"></a>Ta bort en primär server grupp

Om du vill ta bort en primär server grupp använder du samma steg som för att ta bort en Server grupp med fristående citus). 

> [!IMPORTANT]
>
> När du tar bort en primär server grupp stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående server grupper som nu stöder både läsning och skrivning.

Följ dessa steg om du vill ta bort en Server grupp från Azure Portal:

1. I Azure Portal väljer du din primära Azure Database for PostgreSQL Server grupp.

2. Öppna **översikts** sidan för Server gruppen. Välj **Ta bort**.
 
3. Ange namnet på den primära server grupp som ska tas bort. Bekräfta borttagning av den primära server gruppen genom att välja **ta bort** .
 

## <a name="delete-a-replica"></a>Ta bort en replik

Du kan ta bort en Läs replik på samma sätt som du tar bort en primär server grupp.

- I Azure Portal öppnar du sidan **Översikt** för Läs repliken. Välj **Ta bort**.
 
Du kan också ta bort Läs repliken från fönstret **replikering** genom att följa dessa steg:

1. I Azure Portal väljer du din primära citus-Server grupp.

2. Välj **replikering** under **Server grupp hantering** på menyn Server grupp.

3. Välj den Läs replik som ska tas bort.
 
4. Välj **ta bort replik**.
 
5. Ange namnet på repliken som ska tas bort. Bekräfta borttagning av repliken genom att välja **ta bort** .

## <a name="next-steps"></a>Nästa steg

* Läs mer om att [läsa repliker i Azure Database for PostgreSQL-storskalig (citus)](concepts-hyperscale-read-replicas.md).
