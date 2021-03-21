---
title: Konfigurera långsamma fråge loggar – Azure Portal-Azure Database for MySQL – flexibel Server
description: Den här artikeln beskriver hur du konfigurerar och kommer åt långsamma frågemeddelanden i Azure Database for MySQL flexibel Server från Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540071"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurera och komma åt långsamma Query-loggar för Azure Database for MySQL flexibel server med hjälp av Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Du kan konfigurera, lista och ladda ned Azure Database for MySQL flexibla servern [långsamma frågemeddelanden](concepts-slow-query-logs.md) från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar
Anvisningarna i den här artikeln kräver att du har en [flexibel Server](quickstart-create-server-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till MySQLs långsamma fråga logg. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj din flexibla Server.

1. Under avsnittet **Inställningar** på sid panelen väljer du **Server parametrar**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Sidan Server parametrar.":::

1. Uppdatera **slow_query_log** -parametern till **på**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="Aktivera långsamma fråge loggar.":::

1. Ändra eventuella andra parametrar som behövs (t. ex. `long_query_time`, `log_slow_admin_statements`). Se de [långsammaste fråge logg](./concepts-slow-query-logs.md#configure-slow-query-logging) dokumenten för fler parametrar.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Uppdatera relaterade parametrar för förfrågningar om långsam fråga.":::

1. Välj **Spara**. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Spara långsamma fråge logg parametrar.":::

På sidan **Server parametrar** kan du gå tillbaka till listan över loggar genom att stänga sidan.

## <a name="set-up-diagnostics"></a>Konfigurera diagnostik

Långsamma frågeuttryck är integrerade med Azure Monitor diagnostikinställningar så att du kan skicka loggar till Azure Monitor loggar, Event Hubs eller Azure Storage.

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostiska inställningar**  >  **Lägg till diagnostikinställningar**.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="Skärm bild av alternativ för diagnostikinställningar":::

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka destinationer som de långsamma frågeresultaten ska skickas till (lagrings konto, händelsehubben eller Log Analytics arbets yta).

1. Välj **MySqlSlowLogs** som logg typ.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="Skärm bild av konfigurations alternativ för diagnostikinställningar":::

1. När du har konfigurerat data Sinks att skicka in de långsamma frågeresultaten till väljer du **Spara**.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="Skärm bild av konfigurations alternativ för diagnostikinställningar med Spara markerat":::

1. Få åtkomst till långsamma fråga-loggar genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

Om du skickas loggar till Azure Monitor loggar (Log Analytics) kan du läsa några [exempel frågor](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) som du kan använda för analys. 

## <a name="next-steps"></a>Nästa steg
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Läs mer om [långsamma Query-loggar](concepts-slow-query-logs.md)
- Mer information om parameter definitioner och MySQL-loggning finns i MySQL-dokumentationen för [loggar](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Lär dig mer om [gransknings loggar](concepts-audit-logs.md)
