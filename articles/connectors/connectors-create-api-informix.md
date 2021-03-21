---
title: Ansluta till IBM Informix-databas
description: Automatisera aktiviteter och arbets flöden som hanterar resurser som lagras i IBM Informix genom att använda Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761652"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Hantera IBM Informix Database-resurser med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Informix-kopplingen](/connectors/informix/)kan du skapa automatiserade uppgifter och arbets flöden som hanterar resurser i en IBM Informix-databas. Den här anslutningen innehåller en Microsoft-klient som kommunicerar med fjärranslutna Informix-serverdatorer över ett TCP/IP-nätverk, inklusive molnbaserade databaser som IBM Informix för Windows som körs i Azure Virtualization och lokala databaser när du använder den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Du kan ansluta till dessa Informix-plattformar och-versioner om de har kon figurer ATS för att stödja DRDA-klient anslutningar (Distributed Relations databas arkitektur):

* IBM Informix 12,1
* IBM Informix 11,7

Det här avsnittet visar hur du använder-anslutningen i en Logic app för att bearbeta databas åtgärder.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* För lokala databaser [laddar du ned och installerar den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en lokal dator och [skapar sedan en Azure Data gateway-resurs i Azure Portal](../logic-apps/logic-apps-gateway-connection.md).

* Den Logic-app där du behöver åtkomst till din Informix-databas. Den här kopplingen tillhandahåller endast åtgärder, så din Logi Kap par måste redan börja med en utlösare, till exempel [upprepnings utlösaren](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Lägg till en Informix-åtgärd

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer, om den inte redan är öppen.

1. Under steget där du vill lägga till en Informix-åtgärd väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger `informix` du som filter. Från listan åtgärder väljer du den åtgärd som du vill ha, till exempel:

   ![Välj den Informix-åtgärd som ska köras](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Anslutningen tillhandahåller de här åtgärderna, som kör motsvarande databas åtgärder:

   * Hämta tabeller – Visa databas tabeller med hjälp av en `CALL` instruktion
   * Hämta rader – Läs alla rader med hjälp av en `SELECT *` instruktion
   * Hämta rad – Läs en rad med hjälp av en `SELECT WHERE` instruktion
   * Lägg till en rad med hjälp av en `INSERT` instruktion
   * Redigera en rad med hjälp av en `UPDATE` instruktion
   * Ta bort en rad med hjälp av en `DELETE` instruktion

1. Om du uppmanas att ange anslutnings information för din Informix-databas följer du [stegen för att skapa anslutningen](#create-connection)och fortsätter sedan med nästa steg.

1. Ange information för den valda åtgärden:

   | Åtgärd | Beskrivning | Egenskaper och beskrivningar |
   |--------|-------------|-----------------------------|
   | **Hämta tabeller** | Lista databas tabeller genom att köra en Informix-ANROPs instruktion. | Inget |
   | **Hämta rader** | Hämta alla rader i den angivna tabellen genom att köra en Informix- `SELECT *` instruktion. | **Tabell namn**: namnet på den Informix-tabell som du vill använda <p><p>Om du vill lägga till andra egenskaper för den här åtgärden väljer du dem i listan **Lägg till ny parameter** . Mer information finns i [kopplingens referens ämne](/connectors/informix/). |
   | **Hämta rad** | Hämta en rad från den angivna tabellen genom att köra en Informix- `SELECT WHERE` instruktion. | - **Tabell namn**: namnet på den Informix-tabell som du vill använda <br>- **Rad-ID**: det unika ID: t för raden, till exempel `9999` |
   | **Infoga rad** | Lägg till en rad i den angivna Informix-tabellen genom att köra en Informix- `INSERT` instruktion. | - **Tabell namn**: namnet på den Informix-tabell som du vill använda <br>- **objekt**: raden med värdena som ska läggas till |
   | **Uppdatera rad** | Ändra en rad i den angivna Informix-tabellen genom att köra en Informix- `UPDATE` instruktion. | - **Tabell namn**: namnet på den Informix-tabell som du vill använda <br>- **Rad-ID**: det unika ID: t för raden som ska uppdateras, till exempel `9999` <br>- **Rad**: raden med de uppdaterade värdena, till exempel `102` |
   | **Ta bort rad** | Ta bort en rad från den angivna Informix-tabellen genom att köra en Informix- `DELETE` instruktion. | - **Tabell namn**: namnet på den Informix-tabell som du vill använda <br>- **Rad-ID**: det unika ID: t för raden som ska tas bort, till exempel `9999` |
   ||||

1. Spara logikappen. [Testa nu din](#test-logic-app) Logi Kap par eller Fortsätt att skapa din Logic app.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Anslut till Informix

1. Om din Logic App ansluter till en lokal databas väljer du **Anslut via lokal datagateway**.

1. Ange den här anslutnings informationen och välj sedan **skapa**.

   | Egenskap | JSON-egenskap | Obligatorisk | Exempelvärde | Beskrivning |
   |----------|---------------|----------|---------------|-------------|
   | Anslutningsnamn | `name` | Ja | `informix-demo-connection` | Namnet som ska användas för anslutningen till din Informix-databas |
   | Server | `server` | Ja | Kunde `informixdemo.cloudapp.net:9089` <br>-Lokalt: `informixdemo:9089` | TCP/IP-adressen eller aliaset i antingen IPv4-eller IPv6-format, följt av ett kolon och ett TCP/IP-portnummer |
   | Databas | `database` | Ja | `nwind` | DRDA Relations databas namnet (RDBNAM) eller Informix-databasens namn (dbname). Informix accepterar en sträng på 128 byte. |
   | Autentisering | `authentication` | Endast lokalt | **Basic** eller **Windows** (Kerberos) | Autentiseringstypen som krävs av din Informix-databas. Den här egenskapen visas bara när du väljer **Anslut via en lokal datagateway**. |
   | Användarnamn | `username` | Inga | <*databas-användar namn*> | Ett användar namn för databasen |
   | Lösenord | `password` | Inga | <*databas – lösen ord*> | Ett lösen ord för databasen |
   | Gateway | `gateway` | Endast lokalt | – <*Azure – prenumeration*> <br>-<*Azure-on-premises-data-gateway-resurs*> | Azure-prenumerationen och Azure-resursens namn för den lokala datagatewayen som du skapade i Azure Portal. **Gateway** -egenskapen och underordnade egenskaper visas bara när du väljer **Anslut via lokal datagateway**. |
   ||||||

   Exempel:

   * **Moln databas**

     ![Anslutnings information för moln databas](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Lokal databas**

     ![Anslutnings information för lokal databas](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Spara logikappen.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testa din Logic app

1. I verktygsfältet Logic App Designer väljer du **Kör**. När din Logic App körs kan du Visa de utdata som körs.

1. Från din Logic Apps-meny väljer du **Översikt**. I fönstret Översikt   >  väljer du den senaste körningen under Sammanfattning **Kör historik**.

1. Under **Logic app-körning** väljer du **Kör information**.

1. I listan åtgärder väljer du åtgärden med de utdata som du vill visa, till exempel **Get_tables**.

   Om åtgärden lyckades markeras egenskapen **status** som **slutförd**.

1. Om du vill visa indata, under **indata-länk**, väljer du URL-länken. Om du vill visa utdata, under länken **utdata** , väljer du URL-länken. Här följer några exempel på utdata:

   * **Get_tables** visar en lista över tabeller:

     ![Utdata från åtgärden Hämta tabeller](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** visar en lista med rader:

     ![Utdata från åtgärden hämta rader](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** visar den angivna raden:

     ![Utdata från åtgärden Hämta rad](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** visar den nya raden:

     ![Utdata från åtgärden Infoga rad](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** visar den uppdaterade raden:

     ![Utdata från åtgärden Uppdatera rad](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** visar den borttagna raden:

     ![Utdata från åtgärden "ta bort rad"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger beskrivning, finns på [kopplingens referens sida](/connectors/informix/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](apis-list.md)
