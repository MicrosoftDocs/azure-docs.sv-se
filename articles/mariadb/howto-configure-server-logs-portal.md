---
title: Åtkomst till långsamma Query-loggar – Azure Portal-Azure Database for MariaDB
description: Den här artikeln beskriver hur du konfigurerar och kommer åt långsamma frågemeddelanden i Azure Database for MariaDB från Azure Portal.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: f3a01d3e80be35d10ef5c163c9ad8956f37283c1
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662516"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>Konfigurera och få åtkomst till Azure Database för Maria DB långsam fråga från Azure Portal

Du kan konfigurera, lista och ladda ned [Azure Database for MariaDB långsamma frågemeddelanden](concepts-server-logs.md) från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar
Anvisningarna i den här artikeln kräver att du har [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till den långsamma fråge loggen. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure Database for MariaDB-Server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. 
   ![Skärm bild av alternativ för Server loggar](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Om du vill se server parametrarna väljer du **Klicka här för att aktivera loggar och konfigurera logg parametrar**.

5. Aktivera **slow_query_log** till **på**.

6. Välj var loggarna ska matas till med **log_output**. Om du vill skicka loggar till både lokal lagring och Azure Monitor diagnostikloggar väljer du **fil**. 

7. Ändra eventuella andra parametrar som behövs. 

8. Välj **Spara**. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Skärm bild av långsam frågans logg parametrar och spara.":::

På sidan **Server parametrar** kan du gå tillbaka till listan över loggar genom att stänga sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen har påbörjats kan du Visa en lista över tillgängliga långsamma frågemeddelanden och hämta enskilda loggfiler. 

1. Öppna Azure-portalen.

2. Välj din Azure Database for MariaDB-Server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. Sidan visar en lista över loggfilerna.

   ![Skärm bild av sidan Server loggar med lista över loggar markerade](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivnings konventionen för loggen är **MySQL-långsamma-< ditt Server namn>-yyyymmddhh. log**. Datum och tid som används i fil namnet är den tidpunkt då loggen utfärdades. Loggfilerna roteras var 24: e timme eller 7,5 GB, beroende på vilket som kommer först.

4. Om det behövs kan du använda sökrutan för att snabbt begränsa till en specifik logg baserat på datum och tid. Sökningen har samma namn som loggen.

5. Om du vill hämta enskilda loggfiler väljer du nedåtpilen bredvid varje loggfil i tabell raden.

   ![Skärm bild av sidan Server loggar med nedåtpil ikon markerad](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostiska inställningar**  >  **Lägg till diagnostisk inställning**.

   ![Skärm bild av alternativ för diagnostikinställningar](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka data mottagare som de långsamma frågarna ska skickas till (lagrings konto, händelsehubben eller Log Analytics arbets yta).

1. Välj **MySqlSlowLogs** som logg typ.
![Skärm bild av konfigurations alternativ för diagnostikinställningar](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat data Sinks att skicka in de långsamma frågeresultaten till väljer du **Spara**.
![Skärm bild av konfigurations alternativ för diagnostikinställningar med Spara markerat](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Få åtkomst till långsamma fråga-loggar genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du hämtar långsamma frågemeddelanden via programmering finns i [komma åt långsamma fråge loggar i CLI](howto-configure-server-logs-cli.md) .
- Läs mer om [långsamma frågemeddelanden](concepts-server-logs.md) i Azure Database for MariaDB.
- Mer information om parameter definitioner och loggning finns i MariaDB-dokumentationen för [loggar](https://mariadb.com/kb/en/library/slow-query-log-overview/).