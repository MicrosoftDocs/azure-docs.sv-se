---
title: Komma åt och hantera IBM DB2-resurser
description: Läsa, redigera, uppdatera och hantera IBM DB2-resurser genom att skapa automatiserade arbets flöden med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967410"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Få åtkomst till och hantera IBM DB2-resurser med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [IBM DB2 Connector](/connectors/db2/)kan du skapa automatiserade uppgifter och arbets flöden baserat på de resurser som lagras i DB2-databasen. Dina arbets flöden kan ansluta till resurserna i databasen, läsa och lista dina databas tabeller, lägga till rader, ändra rader, ta bort rader och mycket annat. Du kan inkludera åtgärder i dina Logi Kap par som får svar från databasen och göra utdata tillgängliga för andra åtgärder.

Den här artikeln visar hur du kan skapa en Logic app som utför olika databas åtgärder. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plattformar och versioner som stöds

DB2-anslutaren innehåller en Microsoft-klient som kommunicerar med fjär DB2-servrar över ett TCP/IP-nätverk. Du kan använda den här anslutningen för att komma åt moln databaser som IBM DB2 för Windows som körs i Azure Virtualization. Du kan också komma åt lokala DB2-databaser när du [har installerat och konfigurerat den lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md).

IBM DB2 Connector stöder dessa IBM DB2-plattformar och-versioner tillsammans med IBM DB2-kompatibla produkter som stöder DRDA (Distributed Relations databas arkitektur) för SQL Access Manager (SQLAM), version 10 och 11:

| Plattform | Version | 
|----------|---------|
| IBM DB2 för z/OS | 12, 11,1, 10,1 |
| IBM DB2 för i | 7,3, 7,2, 7,1 |
| IBM DB2 för LUW | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Databas åtgärder som stöds

IBM DB2 Connector stöder dessa databas åtgärder som mappar till motsvarande åtgärder i anslutnings tjänsten:

| Databas åtgärd | Kopplings åtgärd |
|--------------------|------------------|
| Lista databas tabeller | Hämta tabeller |
| Läs en rad med SELECT | Hämta rad |
| Läs alla rader med SELECT | Hämta rader |
| Lägg till en rad med hjälp av Infoga | Infoga rad |
| Redigera en rad med UPDATE | Uppdatera rad |
| Ta bort en rad med hjälp av ta bort | Ta bort rad |
|||

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En IBM DB2-databas, antingen molnbaserad eller lokalt

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill få åtkomst till DB2-databasen. Den här kopplingen tillhandahåller endast åtgärder, så för att starta din Logi Kap par väljer du en separat utlösare, till exempel utlösaren **upprepning** .
I exemplen i den här artikeln används **upprepnings** utlösaren.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Lägg till DB2-åtgärd – Hämta tabeller

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer, om den inte redan är öppen.

1. Under utlösaren väljer du **nytt steg**.

1. I rutan Sök anger du "DB2" som filter. I det här exemplet väljer du den här åtgärden i listan åtgärder: **Hämta tabeller (förhands granskning)**

   ![Välj åtgärd](./media/connectors-create-api-db2/select-db2-action.png)

   Nu uppmanas du att ange anslutnings information för DB2-databasen.

1. Följ stegen för att skapa anslutningar för [moln databaser](#cloud-connection) eller [lokala databaser](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Ansluta till Cloud DB2

Om du vill konfigurera din anslutning anger du följande information när du uppmanas till det, väljer **skapa** och sparar sedan din Logic app:

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Anslut via lokal gateway** | Inga | Gäller endast för lokala anslutningar. |
| **Anslutnings namn** | Ja | Namnet på anslutningen, till exempel "MyLogicApp-DB2-Connection" |
| **Server** | Ja | Adressen eller Ali Asets kolon port nummer för DB2-servern, till exempel "myDB2server.cloudapp.net:50000" <p><p>**Obs!** det här värdet är en sträng som representerar en TCP/IP-adress eller ett alias, antingen i IPv4-eller IPv6-format, följt av ett kolon och ett port nummer för TCP/IP. |
| **Databas** | Ja | Namnet på din databas <p><p>**Obs!** det här värdet är en sträng som representerar ett DRDA Relations databas namn (RDBNAM): <p>– DB2 för z/OS accepterar en 16 byte-sträng där databasen kallas "IBM DB2 for z/OS"-plats. <br>-DB2 för jag accepterar en 18-byte-sträng där databasen kallas "IBM DB2 for i" Relations databas. <br>– DB2 för LUW accepterar en 8-byte-sträng. |
| **Användarnamn** | Ja | Ditt användar namn för databasen <p><p>**Obs!** det här värdet är en sträng vars längd baseras på den angivna databasen: <p><p>– DB2 för z/OS accepterar en 8-byte-sträng. <br>– DB2 för jag accepterar en 10 byte-sträng. <br>– DB2 för Linux eller UNIX accepterar en 8-byte-sträng. <br>– DB2 för Windows accepterar en 30 byte-sträng. |
| **Lösenord** | Ja | Ditt lösen ord för databasen |
||||

Exempel:

![Anslutnings information för molnbaserade databaser](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Anslut till lokal DB2

Innan du skapar anslutningen måste du redan ha din lokala datagateway installerad. Annars kan du inte slutföra konfigurationen av anslutningen. Om du har en gateway-installation fortsätter du med att tillhandahålla dessa anslutnings uppgifter och väljer sedan **skapa**.

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Anslut via lokal gateway** | Ja | Gäller när du vill ha en lokal anslutning och visar de lokala anslutnings egenskaperna. |
| **Anslutnings namn** | Ja | Namnet på anslutningen, till exempel "MyLogicApp-DB2-Connection" | 
| **Server** | Ja | Adressen eller Ali Asets kolon port nummer för DB2-servern, till exempel "myDB2server: 50000" <p><p>**Obs!** det här värdet är en sträng som representerar en TCP/IP-adress eller ett alias, antingen i IPv4-eller IPv6-format, följt av ett kolon och ett port nummer för TCP/IP. |
| **Databas** | Ja | Namnet på din databas <p><p>**Obs!** det här värdet är en sträng som representerar ett DRDA Relations databas namn (RDBNAM): <p>– DB2 för z/OS accepterar en 16 byte-sträng där databasen kallas "IBM DB2 for z/OS"-plats. <br>-DB2 för jag accepterar en 18-byte-sträng där databasen kallas "IBM DB2 for i" Relations databas. <br>– DB2 för LUW accepterar en 8-byte-sträng. |
| **Autentisering** | Ja | Autentiseringstypen för anslutningen, till exempel "grundläggande" <p><p>**Obs**: Välj det här värdet i listan, som innehåller Basic eller Windows (Kerberos). |
| **Användarnamn** | Ja | Ditt användar namn för databasen <p><p>**Obs!** det här värdet är en sträng vars längd baseras på den angivna databasen: <p><p>– DB2 för z/OS accepterar en 8-byte-sträng. <br>– DB2 för jag accepterar en 10 byte-sträng. <br>– DB2 för Linux eller UNIX accepterar en 8-byte-sträng. <br>– DB2 för Windows accepterar en 30 byte-sträng. |
| **Lösenord** | Ja | Ditt lösen ord för databasen |
| **Gateway** | Ja | Namnet på din installerade lokala datagateway <p><p>**Obs**: Välj det här värdet i listan, som innehåller alla installerade datagatewayer i din Azure-prenumeration och resurs grupp. |
||||

Exempel:

![Anslutnings information för lokala databaser](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Visa utdatakolumner

Om du vill köra din Logi Kap par manuellt går du till verktygsfältet i designern och väljer **Kör**. När din Logic-app har körts klart kan du se resultatet från körningen.

1. På din Logic app-meny väljer du **Översikt**.

1. Under **Sammanfattning** i avsnittet **körnings historik** väljer du den senaste körningen, som är det första objektet i listan.

   ![Visa körningshistorik](./media/connectors-create-api-db2/run-history.png)

1. Under **Logic app-körning** kan du nu granska status, indata och utdata för varje steg i din Logic app.
Expandera åtgärden **Hämta tabeller** .

   ![Expandera åtgärd](./media/connectors-create-api-db2/expand-action-step.png)

1. Om du vill visa indata väljer du **Visa rå data**.

1. Om du vill visa utdata väljer du **Visa rå data**.

   Utdata innehåller en lista över tabeller.

   ![Visa utdatakolumner](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Hämta rad

Om du vill hämta en post i en DB2 Database-tabell använder du åtgärden **Hämta rad** i din Logic-app. Den här åtgärden kör en DB2 `SELECT WHERE` -instruktion, till `SELECT FROM AREA WHERE AREAID = '99999'` exempel.

1. Om du aldrig har använt DB2-åtgärder före i din Logic-app kan du läsa stegen i avsnittet [Lägg till DB2-åtgärd – Hämta tabeller](#add-db2-action) , men lägga till åtgärden **Hämta rad** i stället och sedan återgå hit för att fortsätta.

   När du har lagt till åtgärden **Hämta rad** visas följande exempel på din Logic-app:

   ![Hämta rad åtgärd](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Ange värden för alla obligatoriska egenskaper (*). När du har valt en tabell visar åtgärden de relevanta egenskaper som är relevanta för poster i tabellen.

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabell namn** | Ja | Den tabell som innehåller den post som du vill ha, till exempel "AREA" i det här exemplet |
   | **Area-ID** | Ja | ID för den post som du vill ha, till exempel "99999" i det här exemplet |
   ||||

   ![Skärm bild som visar åtgärden "Hämta rad (för hands version)" med det öppnade "Tabell namnet"-listan och värdet "AREA" valt.](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

### <a name="view-output-row"></a>Visa utmatnings rad

Om du vill köra din Logi Kap par manuellt går du till verktygsfältet i designern och väljer **Kör**. När din Logic-app har körts klart kan du se resultatet från körningen.

1. På din Logic app-meny väljer du **Översikt**.

1. Under **Sammanfattning** i avsnittet **körnings historik** väljer du den senaste körningen, som är det första objektet i listan.

1. Under **Logic app-körning** kan du nu granska status, indata och utdata för varje steg i din Logic app.
Expandera åtgärden **Hämta rad** .

1. Om du vill visa indata väljer du **Visa rå data**.

1. Om du vill visa utdata väljer du **Visa rå data**.

   Utdata inkluderar den angivna raden.

   ![Visa utmatnings rad](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Hämta rader

Om du vill hämta alla poster i en DB2-databas tabell använder du åtgärden **Hämta rader** i din Logic-app. Den här åtgärden kör en DB2 `SELECT` -instruktion, till `SELECT * FROM AREA` exempel.

1. Om du aldrig har använt DB2-åtgärder före i din Logic-app kan du läsa stegen i avsnittet [Lägg till DB2-åtgärd – Hämta tabeller](#add-db2-action) , men lägga till åtgärden **Hämta rader** i stället och sedan återgå hit för att fortsätta.

   När du har lagt till åtgärden **Hämta rader** visas följande exempel på din Logic-app:

   ![Hämta rader-åtgärd](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Öppna listan **tabell namn** och välj sedan den tabell som du vill ha, vilket är "Area" i det här exemplet:

   ![Skärm bild som visar åtgärden "Hämta rad (för hands version)" med värdet "AREA" som har marker ATS i listan "Tabell namn".](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Välj **Visa avancerade alternativ** om du vill ange ett filter eller en fråga för resultat.

1. När du är klar väljer du **Spara** i verktygsfältet designer.

### <a name="view-output-rows"></a>Visa utdatakolumner

Om du vill köra din Logi Kap par manuellt går du till verktygsfältet i designern och väljer **Kör**. När din Logic-app har körts klart kan du se resultatet från körningen.

1. På din Logic app-meny väljer du **Översikt**.

1. Under **Sammanfattning** i avsnittet **körnings historik** väljer du den senaste körningen, som är det första objektet i listan.

1. Under **Logic app-körning** kan du nu granska status, indata och utdata för varje steg i din Logic app.
Expandera åtgärden **Hämta rader** .

1. Om du vill visa indata väljer du **Visa rå data**.

1. Om du vill visa utdata väljer du **Visa rå data**.

   Utdata innehåller alla poster i den angivna tabellen.

   ![Visa utdatakolumner](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Infoga rad

Om du vill lägga till en enskild post i en DB2-databas tabell använder du åtgärden **Infoga rad** i din Logic-app. Den här åtgärden kör en DB2 `INSERT` -instruktion, till `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` exempel.

1. Om du aldrig har använt DB2-åtgärder innan i din Logic-app kan du läsa stegen i avsnittet [Lägg till DB2-åtgärd – Hämta tabeller](#add-db2-action) , men lägga till åtgärden **Infoga rad** i stället och sedan återgå hit för att fortsätta.

   När du har lagt till åtgärden **Infoga rad** visas följande exempel på din Logic-app:

   ![Åtgärden Infoga rad](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Ange värden för alla obligatoriska egenskaper (*). När du har valt en tabell visar åtgärden de relevanta egenskaper som är relevanta för poster i tabellen.

   I det här exemplet är följande egenskaper:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabell namn** | Ja | Tabellen där posten ska läggas till, till exempel "AREA" |
   | **Area-ID** | Ja | ID för det utrymme som ska läggas till, till exempel "99999" |
   | **Beskrivning av områden** | Ja | Beskrivningen för det utrymme som ska läggas till, till exempel "Area 99999" |
   | **Regions-ID** | Ja | ID för den region som ska läggas till, till exempel "102" |
   |||| 

   Exempel:

   ![Skärm bild som visar Logic Apps designer med åtgärden "Infoga rad (förhands granskning)" och exempel egenskaps värden.](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

### <a name="view-insert-row-outputs"></a>Visa infogning av rad-utdata

Om du vill köra din Logi Kap par manuellt går du till verktygsfältet i designern och väljer **Kör**. När din Logic-app har körts klart kan du se resultatet från körningen.

1. På din Logic app-meny väljer du **Översikt**.

1. Under **Sammanfattning** i avsnittet **körnings historik** väljer du den senaste körningen, som är det första objektet i listan.

1. Under **Logic app-körning** kan du nu granska status, indata och utdata för varje steg i din Logic app.
Expandera åtgärden **Infoga rad** .

1. Om du vill visa indata väljer du **Visa rå data**.

1. Om du vill visa utdata väljer du **Visa rå data**.

   Utdata innehåller den post som du har lagt till i din angivna tabell.

   ![Visa utdata med infogad rad](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Uppdatera rad

Om du vill uppdatera en enskild post i en DB2-databas tabell använder du åtgärden **Uppdatera rad** i din Logic-app. Den här åtgärden kör en DB2 `UPDATE` -instruktion, till `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)` exempel.

1. Om du aldrig har använt DB2-åtgärder innan i din Logic-app kan du läsa stegen i avsnittet [Lägg till DB2-åtgärd – Hämta tabeller](#add-db2-action) , men lägga till åtgärden **Uppdatera rad** i stället och sedan återgå hit för att fortsätta.

   När du har lagt till åtgärden **Uppdatera rad** visas följande exempel på din Logic-app:

   ![Åtgärd för uppdatering av rad](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Ange värden för alla obligatoriska egenskaper (*). När du har valt en tabell visar åtgärden de relevanta egenskaper som är relevanta för poster i tabellen.

   I det här exemplet är följande egenskaper:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabell namn** | Ja | Den tabell där posten ska uppdateras, t. ex. "AREA" |
   | **Rad-ID** | Ja | ID för den post som ska uppdateras, till exempel "99999" |
   | **Area-ID** | Ja | Det nya yt-ID: t, till exempel "99999" |
   | **Beskrivning av områden** | Ja | Beskrivningen av det nya fältet, till exempel "uppdaterad 99999" |
   | **Regions-ID** | Ja | Det nya regions-ID: t, till exempel "102" |
   ||||

   Exempel:

   ![Skärm bild som visar Logic Apps designer med åtgärden Uppdatera rad (förhands granskning) där du väljer en tabell.](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

### <a name="view-update-row-outputs"></a>Visa uppdatering av rad utdata

Om du vill köra din Logi Kap par manuellt går du till verktygsfältet i designern och väljer **Kör**. När din Logic-app har körts klart kan du se resultatet från körningen.

1. På din Logic app-meny väljer du **Översikt**.

1. Under **Sammanfattning** i avsnittet **körnings historik** väljer du den senaste körningen, som är det första objektet i listan.

1. Under **Logic app-körning** kan du nu granska status, indata och utdata för varje steg i din Logic app.
Expandera åtgärden **Uppdatera rad** .

1. Om du vill visa indata väljer du **Visa rå data**.

1. Om du vill visa utdata väljer du **Visa rå data**.

   Utdata innehåller posten du uppdaterade i den angivna tabellen.

   ![Visa utdata med uppdaterad rad](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Ta bort rad

Om du vill ta bort en enskild post från en DB2-databas tabell använder du åtgärden **ta bort rad** i din Logic-app. Den här åtgärden kör en DB2 `DELETE` -instruktion, till `DELETE FROM AREA WHERE AREAID = '99999'` exempel.

1. Om du aldrig har använt DB2-åtgärder innan i din Logic-app kan du läsa stegen i avsnittet [Lägg till DB2-åtgärd – Hämta tabeller](#add-db2-action) , men lägga till åtgärden **ta bort rad** i stället och sedan återgå hit för att fortsätta.

   När du har lagt till åtgärden **ta bort rad** visas följande exempel på din Logic-app:

   ![Åtgärd för att ta bort rad](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Ange värden för alla obligatoriska egenskaper (*). När du har valt en tabell visar åtgärden de relevanta egenskaper som är relevanta för poster i tabellen.

   I det här exemplet är följande egenskaper:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabell namn** | Ja | Den tabell där posten ska tas bort, till exempel "AREA" |
   | **Rad-ID** | Ja | ID för den post som ska tas bort, till exempel "99999" |
   ||||

   Exempel:

   ![Skärm bild som visar Logic Apps designer med åtgärden "ta bort rad (förhands granskning)" där du väljer en tabell som ska tas bort.](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

### <a name="view-delete-row-outputs"></a>Visa ta bort rad utdata

Om du vill köra din Logi Kap par manuellt går du till verktygsfältet i designern och väljer **Kör**. När din Logic-app har körts klart kan du se resultatet från körningen.

1. På din Logic app-meny väljer du **Översikt**.

1. Under **Sammanfattning** i avsnittet **körnings historik** väljer du den senaste körningen, som är det första objektet i listan.

1. Under **Logic app-körning** kan du nu granska status, indata och utdata för varje steg i din Logic app.
Expandera åtgärden **ta bort rad** .

1. Om du vill visa indata väljer du **Visa rå data**.

1. Om du vill visa utdata väljer du **Visa rå data**.

   Utmatningarna innehåller inte längre posten som du tog bort från din angivna tabell.

   ![Visa utdata utan raderad rad](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/db2/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

