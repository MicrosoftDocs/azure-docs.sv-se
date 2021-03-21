---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar MariaDB-Server parametrar i Azure Database for MariaDB att använda Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 7081535bb709e6731a9a15436334e8742e7bdd08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664808"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for MariaDB att använda Azure Portal

Azure Database for MariaDB stöder konfiguration av vissa Server parametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure Portal. Det går inte att justera alla Server parametrar.

>[!Note]
> Serverparametrar kan uppdateras globalt på servernivå via [Azure CLI](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) eller [Azure-portalen](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurera serverparametrar

1. Logga in på Azure Portal och leta sedan upp Azure Database for MariaDB-servern.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MariaDB-servern.
![Sidan Azure Portal Server parametrar](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Leta upp de inställningar du behöver för att justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
![Räkna upp listruta](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på  **Spara** för att spara ändringarna.
![Spara eller ta bort ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Ange parametrar som inte listas

Om den server parameter som du vill uppdatera inte visas i Azure Portal, kan du välja att ange parametern på anslutnings nivå med `init_connect` . Detta anger Server parametrarna för varje klient som ansluter till servern. 

1. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MariaDB-servern.
2. Sök efter `init_connect`
3. Lägg till Server parametrarna i formatet: `SET parameter_name=YOUR_DESIRED_VALUE` i värde kolumnen värde.

    Du kan till exempel ändra teckenuppsättningen för servern genom att ställa in på `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Klicka på **Spara** för att spara dina ändringar.

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Du kan fylla i tids zons tabellerna på servern genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som mysql-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva inaktivera läget för säker uppdatering först med `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).
Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Tids zonen på global nivå kan ställas in från sidan **Server parametrar** i Azure Portal. I nedan anges den globala tids zonen till värdet "US/Pacific".

![Ange tids zons parameter](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att köra `SET time_zone` kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MariaDB-dokumentationen hittar du [datum-och tids funktioner](https://mariadb.com/kb/en/library/convert_tz/).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Server parametrar](concepts-server-parameters.md)
