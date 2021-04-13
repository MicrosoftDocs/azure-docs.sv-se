---
title: Utöka lagring automatiskt – Azure Portal – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du kan aktivera lagring med automatisk Azure Database for MariaDB med Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366121"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Utöka lagring automatiskt i Azure Database for MariaDB med hjälp av Azure Portal
Den här artikeln beskriver hur du kan konfigurera en Azure Database for MariaDB-serverlagring så att den växer utan att påverka arbetsbelastningen.

När en server når den allokerade lagringsgränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk ökning av lagring, ökar serverlagringen för att hantera växande data. För servrar med mindre än 100 GB etablerat lagringsutrymme ökas den etablerade lagringsstorleken med 5 GB så snart det lediga lagringsutrymmet är lägre än 1 GB eller 10 % av det etablerade lagringsutrymmet. För servrar med mer än 100 GB etablerat lagringsutrymme ökar den etablerade lagringsstorleken med 5 % när det lediga utrymmet är mindre än 10 GB av den etablerade lagringsstorleken. De maximala lagringsgränser som anges [här](concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här i guiden behöver du:
- En [Azure Database for MariaDB server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk lagringsfördrering 

Följ de här stegen för att ange att MariaDB-serverlagringen ska växa automatiskt:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MariaDB server.

2. På sidan MariaDB-server, under **Inställningar,** klickar du **på Prisnivå** för att öppna sidan prisnivå.

3. I avsnittet Automatisk tillväxt väljer du Ja för **att aktivera** automatisk ökning av lagring.

    ![Azure Database for MariaDB – Settings_Pricing_tier – Automatisk tillväxt](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att automatisk tillväxt har aktiverats.

    ![Azure Database for MariaDB – automatisk tillväxt](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om hur du skapar aviseringar för mått](howto-alert-metric.md).
