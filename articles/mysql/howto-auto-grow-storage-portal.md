---
title: Utöka lagring automatiskt – Azure Portal – Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan aktivera lagring för automatisk tillväxt för Azure Database for MySQL med Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: afa62d6e3ec93c18596ee5870361bf3b657619d1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365033"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Utöka lagring automatiskt i Azure Database for MySQL med hjälp av Azure Portal
Den här artikeln beskriver hur du kan konfigurera en Azure Database for MySQL serverlagring så att den växer utan att arbetsbelastningen påverkas.

När en server når den allokerade lagringsgränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk lagringsökning ökar serverlagringen för att hantera växande data. För servrar med mindre än 100 GB etablerat lagringsutrymme ökar den etablerade lagringsstorleken med 5 GB så snart det lediga lagringsutrymmet är lägre än 1 GB eller 10 % av det etablerade lagringsutrymmet. För servrar med mer än 100 GB etablerat lagringsutrymme ökar den etablerade lagringsstorleken med 5 % när det lediga utrymmet är mindre än 10 GB av den etablerade lagringsstorleken. De maximala lagringsgränser som anges [här](./concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här guiden behöver du:
- En [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk lagringsfördrering 

Följ de här stegen för att ställa in automatisk utlagring för MySQL-server:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL server.

2. På sidan MySQL-server, under **Inställningar,** klickar du **på Prisnivå** för att öppna sidan Prisnivå.

3. I avsnittet Automatisk tillväxt väljer du Ja för **att aktivera** automatisk ökning av lagring.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL – Settings_Pricing_tier – Automatisk tillväxt":::

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att automatisk tillväxt har aktiverats.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL – automatisk tillväxt":::

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om hur du skapar aviseringar för mått](howto-alert-on-metric.md).
