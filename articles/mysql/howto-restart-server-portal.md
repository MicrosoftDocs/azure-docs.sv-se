---
title: Starta om Server – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan starta om en Azure Database for MySQL-server med hjälp av Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 2d19c0bd4ef5c49b8be82ffa11115ff6a1c6b302
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541801"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Starta om Azure Database for MySQL server med Azure Portal
I det här avsnittet beskrivs hur du kan starta om en Azure Database for MySQL-server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Tiden som krävs för att slutföra en omstart beror på MySQL-återställnings processen. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Utför omstart av Server

Följande steg startar om MySQL-servern:

1. I Azure Portal väljer du Azure Database for MySQL-servern.

2. I verktygsfältet på serverns **översikts** sida klickar du på **starta om**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL-översikt – knappen starta om":::

3. Bekräfta att du vill starta om servern genom att klicka på **Ja** .

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Bekräfta Azure Database for MySQL omstart":::

4. Observera att Server statusen ändras till "omstart".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL-restart-status":::

5. Bekräfta att servern har startats om.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL-omstart lyckades":::

## <a name="next-steps"></a>Nästa steg

[Snabb start: Skapa Azure Database for MySQL server med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
