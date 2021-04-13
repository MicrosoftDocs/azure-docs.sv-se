---
title: Utöka lagring automatiskt – Azure Portal – Azure Database for PostgreSQL – enskild server
description: I den här artikeln beskrivs hur du kan konfigurera automatisk tillväxt för lagring med hjälp Azure Portal i Azure Database for PostgreSQL – enskild server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366138"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Utöka lagring automatiskt med hjälp av Azure Portal i Azure Database for PostgreSQL – enskild server
Den här artikeln beskriver hur du kan konfigurera en Azure Database for PostgreSQL-serverlagring så att den växer utan att påverka arbetsbelastningen.

När en server når den allokerade lagringsgränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk ökning av lagring, ökar serverlagringen för att hantera växande data. För servrar med mindre än 100 GB etablerat lagringsutrymme ökas den etablerade lagringsstorleken med 5 GB så snart det lediga lagringsutrymmet är lägre än 1 GB eller 10 % av det etablerade lagringsutrymmet. För servrar med mer än 100 GB etablerat lagringsutrymme ökar den etablerade lagringsstorleken med 5 % när det lediga utrymmet är mindre än 10 GB av den etablerade lagringsstorleken. De maximala lagringsgränser som anges [här](./concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här i guiden behöver du:
- En [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk lagringsfördrering 

Följ de här stegen för att ange automatisk tillväxt för PostgreSQL-serverlagring:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for PostgreSQL server.

2. På sidan PostgreSQL-server under **Inställningar** klickar du **på Prisnivå** för att öppna sidan prisnivå.

3. I avsnittet **Automatisk tillväxt väljer** du Ja för att **aktivera** automatisk ökning av lagring.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL – Settings_Pricing_tier – Automatisk tillväxt":::

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att automatisk tillväxt har aktiverats.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL – automatisk tillväxt":::

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om hur du skapar aviseringar för mått](howto-alert-on-metric.md).
