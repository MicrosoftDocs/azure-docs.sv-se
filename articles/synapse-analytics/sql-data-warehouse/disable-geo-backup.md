---
title: Inaktivera geo-säkerhetskopieringar
description: Instruktions guide för att inaktivera geo-säkerhetskopieringar för en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739149"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Inaktivera geo-säkerhetskopieringar för en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

I den här artikeln lär du dig att inaktivera geo-säkerhetskopieringar för din dedikerade SQL-pool (tidigare SQL DW) Azure Portal.

## <a name="disable-geo-backups-through-azure-portal"></a>Inaktivera geo-säkerhetskopieringar via Azure Portal

Följ dessa steg om du vill inaktivera geo-säkerhetskopieringar för din dedikerade SQL-pool (tidigare SQL DW):

> [!NOTE]
> Om du inaktiverar geo-säkerhetskopieringar kommer du inte längre att kunna återställa din dedikerade SQL-pool (tidigare SQL DW) till en annan Azure-region. 
>

1. Logga in på ditt [Azure Portal](https://portal.azure.com/) -konto.
1. Välj den dedikerade SQL-pool (tidigare SQL DW)-resurs som du vill inaktivera geo-säkerhetskopieringar för. 
1. Under **Inställningar** i den vänstra navigerings panelen väljer du princip för **geo-säkerhetskopiering**.

   ![Inaktivera geo-säkerhetskopiering](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Om du vill inaktivera geo-säkerhetskopiering väljer du **inaktive rad**. 

   ![Inaktive rad geo-säkerhetskopiering](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Välj *Spara* för att se till att inställningarna sparas. 

   ![Spara inställningar för geo-säkerhetskopiering](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa en borttagen dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
