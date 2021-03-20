---
title: Stoppa/starta – Azure Portal-Azure Database for MySQL Server
description: I den här artikeln beskrivs hur du stoppar/startar åtgärder i Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516880"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Stoppa/starta en Azure Database for MySQL

> [!IMPORTANT]
>  När du **stoppar** servern förblir den kvar i det läget under de kommande 7 dagarna i en storlek. Om du inte **startar** den manuellt under den här tiden startas servern automatiskt i slutet av sju dagar. Du kan välja att **stoppa** det igen om du inte använder servern.

Den här artikeln innehåller steg-för-steg-anvisningar för att stoppa och starta den enskilda servern.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for MySQL enskild server.

> [!NOTE]
> Se begränsningen för att använda [stoppa/starta](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Stoppa/starta Azure Database for MySQL med Azure Portal

### <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den MySQL-server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL stoppa Server":::

    > [!NOTE]
    > När servern har stoppats är de andra hanterings åtgärderna inte tillgängliga för den enskilda servern.

### <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den enda server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **Start** i verktygsfältet.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL Start Server":::

    > [!NOTE]
    > När servern har startats är alla hanterings åtgärder nu tillgängliga för den enskilda servern.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Stoppa/starta Azure Database for MySQL med CLI

### <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den MySQL-server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > När servern har stoppats är de andra hanterings åtgärderna inte tillgängliga för den enskilda servern.

### <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den enda server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **Start** i verktygsfältet.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > När servern har startats är alla hanterings åtgärder nu tillgängliga för den enskilda servern.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
