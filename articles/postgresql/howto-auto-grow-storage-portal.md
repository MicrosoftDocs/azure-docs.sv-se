---
title: Utöka lagringen automatiskt – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan konfigurera automatisk storleks ökning för lagring med hjälp av Azure Portal i Azure Database for PostgreSQL-enskild server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: d49047e9b3a65da90f4a5ac9e4b3cb1e32bd59a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489990"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Utöka lagringen automatiskt med hjälp av Azure Portal i Azure Database for PostgreSQL-enskild server
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL Server lagring så att den växer utan att arbets belastningen påverkas.

När en server når den tilldelade lagrings gränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk storleks ökning ökar server lagringen för att rymma växande data. För servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än eller lika med 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 5% av den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](./concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk storleks ökning för lagring 

Följ de här stegen för att ställa in PostgreSQL Server Storage Auto:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for postgresql-server.

2. På sidan PostgreSQL Server under **Inställningar** klickar du på **pris nivå** för att öppna sidan pris nivå.

3. I avsnittet **automatisk utökning** väljer du **Ja** för att aktivera automatisk storleks ökning.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL-Settings_Pricing_tier-Auto-EXPTREND":::

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att den automatiska utökningen har Aktiver ATS.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL-automatisk tillväxt lyckades":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).