---
title: Starta om Server – Azure Portal-Azure Database for MariaDB
description: I den här artikeln beskrivs hur du startar om en Azure Database for MariaDB-server med hjälp av Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 0c16cf8d67394f6aa50cb30a44b4ff5a677159de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664893"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Starta om Azure Database for MariaDB server med Azure Portal
I det här avsnittet beskrivs hur du kan starta om en Azure Database for MariaDB-Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Tiden som krävs för att slutföra en omstart beror på återställnings processen för MariaDB. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MariaDB-Server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Utför omstart av Server

Följande steg startar om MariaDB-servern:

1. I Azure Portal väljer du Azure Database for MariaDB-servern.

2. I verktygsfältet på serverns **översikts** sida klickar du på **starta om**.

   ![Azure Database for MariaDB-översikt – knappen starta om](./media/howto-restart-server-portal/2-server.png)

3. Bekräfta att du vill starta om servern genom att klicka på **Ja** .

   ![Bekräfta Azure Database for MariaDB omstart](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att Server statusen ändras till "omstart".

   ![Azure Database for MariaDB-restart-status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta att servern har startats om.

   ![Azure Database for MariaDB-omstart lyckades](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

[Snabb start: Skapa Azure Database for MariaDB server med Azure Portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)