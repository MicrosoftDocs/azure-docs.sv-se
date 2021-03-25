---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MySQL flexibel Server
description: I den här artikeln beskrivs hur du konfigurerar MySQL-server parametrar i Azure Database for MySQL flexibel server med hjälp av Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 618862e12bd62fbe37ef5e621c89babd7942c04b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106963"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for MySQL-flexibel server med hjälp av Azure Portal

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Du kan hantera Azure Database for MySQL flexibel Server konfiguration med hjälp av Server parametrar. Server parametrarna konfigureras med standard och Rekommenderat värde när du skapar servern.  

Den här artikeln beskriver hur du visar och konfigurerar Server parametrar med hjälp av Azure Portal. Server parameter bladet på Azure Portal visar både en server parameter som går att ändra och som inte ändras. De Server parametrar som inte kan ändras är nedtonade.

>[!Note]
> Du kan uppdatera Server parametrarna globalt på server nivå med hjälp av [Azure CLI](./how-to-configure-server-parameters-cli.md) eller [Azure Portal](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Konfigurera serverparametrar

1. Logga in på [Azure Portal](https://portal.azure.com)och leta sedan upp Azure Database for MySQL flexibla servern.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för den Azure Database for MySQL flexibla servern.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Sidan Azure Portal Server parametrar":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Leta upp eventuella Server parametrar som du behöver justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Räkna upp listruta":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Klicka på  **Spara** för att spara ändringarna.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Spara eller ta bort ändringar":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. De statiska parametrarna är de som krävs för att servern ska börja gälla. Om du ändrar den statiska parametern uppmanas du att **starta om nu** eller **starta om senare**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Starta om den statiska parametern Spara":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Återställ alla till standard":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Ange server parametrar som inte går att ändra

Om den server parameter som du vill uppdatera inte kan ändras kan du ange parametern på anslutnings nivån med `init_connect` . Detta anger Server parametrarna för varje klient som ansluter till servern. 

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
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Tids zonen på global nivå kan ställas in från sidan **Server parametrar** i Azure Portal. I nedan anges den globala tids zonen till värdet "US/Pacific".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Ange tids zons parameter":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att köra `SET time_zone` kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- Så här konfigurerar du [Server parametrar i Azure CLI](./how-to-configure-server-parameters-cli.md)
