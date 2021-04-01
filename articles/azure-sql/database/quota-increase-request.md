---
title: Begär en kvot ökning
description: Den här sidan beskriver hur du skapar en support förfrågan för att öka kvoterna för Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 27719663acfbdbcd7293defc4b746153359adb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251865"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Begärans kvot ökar för Azure SQL Database-och SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här artikeln förklarar hur du begär en kvot ökning för Azure SQL Database och Azure SQL-hanterad instans. Det beskriver också hur du aktiverar prenumerations åtkomst till en region och hur du begär att aktivera en speciell maskin vara i en region.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Skapa en ny supportbegäran

Använd följande steg för att skapa en ny supportbegäran från Azure Portal för SQL Database.

1. På [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Länken Hjälp + Support](./media/quota-increase-request/help-plus-support.png)

1. I **Hjälp + Support** väljer du **ny supportbegäran**.

    ![Skapa en ny supportbegäran](./media/quota-increase-request/new-support-request.png)

1. För **typ av problem** väljer du **begränsningar för tjänsten och prenumerationen (kvoter)**.

   ![Välj en typ av problem](./media/quota-increase-request/select-quota-issue-type.png)

1. För **prenumeration** väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration för ökad kvot](./media/quota-increase-request/select-subscription-support-request.png)

1. För **typ av kvot** väljer du någon av följande kvot typer:

   - **SQL Database** för kvoter för enkel databas och elastisk pool.
   - **SQL Database Hanterad instans** för hanterade instanser.

   Välj sedan **Nästa: lösningar >>**.

   ![Välj en kvot typ](./media/quota-increase-request/select-quota-type.png)

1. I **informations** fönstret väljer du **Ange information** för att ange ytterligare information.

   ![Ange informations länk](./media/quota-increase-request/provide-details-link.png)

Om du klickar på **Ange information** visas fönstret med **kvot information** som gör att du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika alternativen för **SQL Database** och **SQL Database hanterade instans** kvot typer.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL Database kvot typer

I följande avsnitt beskrivs alternativ för att öka kvoten för **SQL Database** kvot typer:

- Databas transaktions enheter (DTU: er) per server
- Servrar per prenumeration
- Regions åtkomst för prenumerationer eller speciell maskin vara

### <a name="database-transaction-units-dtus-per-server"></a>Databas transaktions enheter (DTU: er) per server

Använd följande steg för att begära en ökning av DTU: er per server.

1. Välj kvot typ för **databas transaktions enheter (DTU: er) per server** .

1. I listan **resurs** väljer du den resurs som ska riktas mot målet.

1. I fältet **ny kvot** anger du den nya DTU-gränsen som du begär.

   ![Information om DTU-kvot](./media/quota-increase-request/quota-details-dtus.png)

Mer information finns i [resurs gränser för enskilda databaser med hjälp av DTU-inköps modell](resource-limits-dtu-single-databases.md) och [resursers gränser för elastiska pooler med hjälp av inköps modellen DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servrar per prenumeration

Använd följande steg för att begära en ökning av antalet servrar per prenumeration.

1. Välj kvot typ för **servrar per prenumeration** .

1. I listan **plats** väljer du den Azure-region som du vill använda. Kvoten är per prenumeration i varje region.

1. I fältet **ny kvot** anger du din begäran om maximalt antal servrar i den regionen.

   ![Information om servrars kvot](./media/quota-increase-request/quota-details-servers.png)

Mer information finns i [SQL Database resurs gränser och resurs styrning](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Aktivera prenumerations åtkomst till en region

Vissa erbjudande typer är inte tillgängliga i varje region. Du kan se ett fel meddelande, till exempel följande:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Om din prenumeration behöver åtkomst i en viss region väljer du alternativet **region åtkomst** . I din begäran anger du det erbjudande och den SKU-information som du vill aktivera för regionen. Om du vill utforska erbjudande-och SKU-alternativen kan du läsa [Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Välj kvot typen **region åtkomst** .

1. I listan **Välj en plats** väljer du den Azure-region som du vill använda. Kvoten är per prenumeration i varje region.

1. Ange **inköps modellen** och **förväntad förbruknings** information.

   ![Åtkomst till region för begäran](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Begäran aktivera speciell maskin vara i en region

Om du vill använda en [maskin varu generation](service-tiers-vcore.md#hardware-generations) som inte är tillgänglig i din region (se [maskin varu tillgänglighet](service-tiers-vcore.md#hardware-availability)) kan du begära det med hjälp av följande steg.

1. Välj den **andra kvot typen kvot förfrågan** .

1. I fältet **Beskrivning** , ange din begäran, inklusive namnet på maskin varu genereringen och namnet på den region som du behöver den i.

   ![Begär maskin vara i en ny region](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Skicka din begäran

Det sista steget är att fylla i återstående information om din SQL Database-kvotbegäran. Välj sedan **Nästa: Granska + skapa>>** . När du har gått igenom begärandeuppgifterna klickar du på **Skapa** för att skicka begäran.

## <a name="next-steps"></a>Nästa steg

När du har skickat in begäran kommer den att granskas. Du kontaktas med ett svar baserat på den information som du angav i formuläret.

Mer information om andra Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
