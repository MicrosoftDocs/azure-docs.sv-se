---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar MySQL-serverns parametrar i Azure Database for MySQL att använda Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 363be8b34f230b812bc24276e1f3925faf0cdc1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540849"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for MySQL att använda Azure Portal

Azure Database for MySQL stöder konfiguration av vissa Server parametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure Portal. Det går inte att justera alla Server parametrar.

>[!Note]
> Serverparametrar kan uppdateras globalt på servernivå via [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) eller [Azure-portalen](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurera serverparametrar

1. Logga in på [Azure Portal](https://portal.azure.com)och leta sedan upp Azure Database for MySQL-servern.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MySQL-servern.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Sidan Azure Portal Server parametrar":::
3. Leta upp de inställningar du behöver för att justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Räkna upp listruta":::
4. Klicka på  **Spara** för att spara ändringarna.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Spara eller ta bort ändringar":::
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Återställ alla till standard":::

## <a name="setting-parameters-not-listed"></a>Ange parametrar som inte listas

Om den server parameter som du vill uppdatera inte visas i Azure Portal, kan du välja att ange parametern på anslutnings nivå med `init_connect` . Detta anger Server parametrarna för varje klient som ansluter till servern. 

1. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MySQL-servern.
2. Sök efter `init_connect`
3. Lägg till Server parametrarna i formatet: `SET parameter_name=YOUR_DESIRED_VALUE` i värde kolumnen värde.

    Du kan till exempel ändra teckenuppsättningen för servern genom att ställa in på `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Klicka på **Spara** för att spara dina ändringar.

>[!Note]
> `init_connect` kan användas för att ändra parametrar som inte kräver superprivilegier på sessionsnivå. Kontrollera om du kan ange parametern med `init_connect` genom att köra kommandot `set session parameter_name=YOUR_DESIRED_VALUE;`. Om kommandot returnerar ett fel av typen **Åtkomst nekad; du behöver superprivilegier**, betyder det att du inte kan ange parametern med ”init_connect”.

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

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Ange tids zons parameter":::

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att köra `SET time_zone` kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- [Anslutnings bibliotek för Azure Database for MySQL](concepts-connection-libraries.md).
