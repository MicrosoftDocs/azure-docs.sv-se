---
title: Åtkomst till långsamma Query-loggar – Azure Portal-Azure Database for MySQL
description: Den här artikeln beskriver hur du konfigurerar och kommer åt långsamma loggar i Azure Database for MySQL från Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496226"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurera och komma åt långsamma Query-loggar från Azure Portal

Du kan konfigurera, lista och ladda ned [Azure Database for MySQL långsamma frågemeddelanden](concepts-server-logs.md) från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar
Anvisningarna i den här artikeln kräver att du har [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till MySQLs långsamma fråga logg. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure Database for MySQL-server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Skärm bild av alternativ för Server loggar":::

4. Om du vill se server parametrarna väljer du **Klicka här för att aktivera loggar och konfigurera logg parametrar**.

5. Aktivera **slow_query_log** till **på**.

6. Välj var loggarna ska matas till med **log_output**. Om du vill skicka loggar till både lokal lagring och Azure Monitor diagnostikloggar väljer du **fil**.

7. Överväg att ange "long_query_time" som representerar tids tröskelvärdet för frågor som ska samlas in i logg filen för långsamma frågor, och de lägsta och standardvärdena för long_query_time är 0 respektive 10.

8. Justera andra parametrar, till exempel log_slow_admin_statements att logga administrativa instruktioner. Som standard loggas inte administrativa instruktioner, inte heller frågor som inte använder index för sökningar. 

9. Välj **Spara**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Skärm bild av långsam frågans logg parametrar och spara.":::

På sidan **Server parametrar** kan du gå tillbaka till listan över loggar genom att stänga sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen har påbörjats kan du Visa en lista över tillgängliga långsamma frågemeddelanden och hämta enskilda loggfiler.

1. Öppna Azure-portalen.

2. Välj din Azure Database for MySQL-server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. Sidan visar en lista över loggfilerna.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Skärm bild av sidan Server loggar med lista över loggar markerade":::

   > [!TIP]
   > Namngivnings konventionen för loggen är **MySQL-långsamma-< ditt Server namn>-yyyymmddhh. log**. Datum och tid som används i fil namnet är den tidpunkt då loggen utfärdades. Loggfilerna roteras var 24: e timme eller 7,5 GB, beroende på vilket som kommer först. 

4. Om det behövs kan du använda sökrutan för att snabbt begränsa till en specifik logg baserat på datum och tid. Sökningen har samma namn som loggen.

5. Om du vill hämta enskilda loggfiler väljer du nedåtpilen bredvid varje loggfil i tabell raden.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Skärm bild av sidan Server loggar med nedåtpil ikon markerad":::

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostiska inställningar**  >  **Lägg till diagnostikinställningar**.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Skärm bild av alternativ för diagnostikinställningar":::

2. Ange ett namn på en diagnostisk inställning.

3. Ange vilka data mottagare som de långsamma frågarna ska skickas till (lagrings konto, händelsehubben eller Log Analytics arbets yta).

4. Välj **MySqlSlowLogs** som logg typ.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Skärm bild av konfigurations alternativ för diagnostikinställningar":::

5. När du har konfigurerat data Sinks att skicka in de långsamma frågeresultaten till väljer du **Spara**.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Skärm bild av konfigurations alternativ för diagnostikinställningar med Spara markerat":::

6. Få åtkomst till långsamma fråga-loggar genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du hämtar långsamma frågemeddelanden via programmering finns i [komma åt långsamma fråge loggar i CLI](howto-configure-server-logs-in-cli.md) .
- Läs mer om [långsamma frågemeddelanden](concepts-server-logs.md) i Azure Database for MySQL.
- Mer information om parameter definitioner och MySQL-loggning finns i MySQL-dokumentationen för [loggar](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).