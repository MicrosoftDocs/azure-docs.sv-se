---
title: Starta om Server-Azure Portal-Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du kan starta om en Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: 1a1afabd606df70ec60cf4fa7c8530ff95a0564f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604863"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Starta om Azure Database for PostgreSQL – en server med hjälp av Azure Portal
I det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.
 
> [!NOTE] 
> Tiden som krävs för att slutföra en omstart beror på återställnings processen för PostgreSQL. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om. Du kanske också vill öka kontroll punkts frekvensen. Du kan också justera kontroll punkter relaterade parameter värden `max_wal_size` , inklusive. Vi rekommenderar också att du kör `CHECKPOINT` kommandot innan du startar om servern.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Utför omstart av Server

Följande steg startar om PostgreSQL-servern:

1. I [Azure Portal](https://portal.azure.com/)väljer du Azure Database for PostgreSQL-servern.

2. I verktygsfältet på serverns **översikts** sida klickar du på **starta om**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL-översikt – knappen starta om":::

3. Bekräfta att du vill starta om servern genom att klicka på **Ja** .

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Bekräfta Azure Database for PostgreSQL omstart":::

4. Observera att Server statusen ändras till "omstart".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL-restart-status":::

5. Bekräfta att servern har startats om.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL-omstart lyckades":::

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du ställer in parametrar i Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)
