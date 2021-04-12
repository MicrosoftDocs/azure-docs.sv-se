---
title: Starta om Server-Azure Portal-Azure Database for MySQL-flexibel Server
description: I den här artikeln beskrivs hur du kan starta om en Azure Database for MySQL flexibel server med hjälp av Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542668"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Starta om Azure Database for MySQL flexibel server med Azure Portal
I det här avsnittet beskrivs hur du kan starta om en Azure Database for MySQL flexibel Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Tiden som krävs för att slutföra en omstart beror på MySQL-återställnings processen. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL flexibel Server](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Utför omstart av Server

Följande steg startar om MySQL-servern:

1. I Azure Portal väljer du Azure Database for MySQL flexibel Server.

2. I verktygsfältet på serverns **översikts** sida klickar du på **starta om**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL-översikt – knappen starta om":::

3. Bekräfta att du vill starta om servern genom att klicka på **Ja** .

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Bekräfta Azure Database for MySQL omstart":::

4. Observera att Server statusen ändras till "omstart".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL-restart-status":::

5. Bekräfta att servern har startats om.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL-omstart lyckades":::

## <a name="next-steps"></a>Nästa steg

[Snabb start: Skapa Azure Database for MySQL flexibel server med Azure Portal](quickstart-create-server-portal.md)
