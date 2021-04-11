---
title: Utöka lagringen automatiskt – Azure Portal – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du kan aktivera lagring med automatisk storleks ökning för Azure Database for MariaDB med Azure Portal
author: rothja
ms.author: jroth
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: c310d55db0b30a30d3964df622f7bb112f4e7552
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552146"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Utöka lagringen automatiskt i Azure Database for MariaDB att använda Azure Portal
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MariaDB Server lagring så att den växer utan att arbets belastningen påverkas.

När en server når den tilldelade lagrings gränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk storleks ökning ökar server lagringen för att rymma växande data. För servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än eller lika med 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 5% av den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MariaDB-Server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk storleks ökning för lagring 

Följ de här stegen för att ställa in MariaDB Server Storage Auto:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MariaDB-Server.

2. På sidan MariaDB-Server under **inställnings** rubrik klickar du på **pris nivå** för att öppna sidan pris nivå.

3. I avsnittet automatisk utökning väljer du **Ja** för att aktivera automatisk storleks ökning.

    ![Azure Database for MariaDB-Settings_Pricing_tier-Auto-EXPTREND](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att den automatiska utökningen har Aktiver ATS.

    ![Azure Database for MariaDB-automatisk tillväxt lyckades](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-metric.md).
