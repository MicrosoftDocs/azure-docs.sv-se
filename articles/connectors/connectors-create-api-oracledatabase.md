---
title: Anslut till Oracle Database
description: 'Infoga och hantera poster med Oracle Database REST-API: er och Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674816"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Kom igång med Oracle Database-anslutningsprogrammet

Med hjälp av Oracle Database Connector skapar du organisations arbets flöden som använder data i den befintliga databasen. Den här anslutningen kan ansluta till en lokal Oracle Database eller en virtuell Azure-dator med Oracle Database installerat. Med den här anslutningen kan du:

* Skapa ett arbets flöde genom att lägga till en ny kund i en kund databas eller uppdatera en order i en order databas.
* Använd åtgärder för att hämta en rad med data, infoga en ny rad och sedan ta bort. Till exempel när en post skapas i Dynamics CRM Online (en utlösare) infogar du en rad i en Oracle Database (en åtgärd). 

Den här anslutningen har inte stöd för följande objekt:

* Vyer 
* Valfri tabell med sammansatta nycklar
* Kapslade objekt typer i tabeller
* Databas funktioner med icke-skalära värden

Den här artikeln visar hur du använder Oracle Database Connector i en Logic app.

## <a name="prerequisites"></a>Förutsättningar

* Oracle-versioner som stöds: 
    * Oracle 9 och senare
    * Oracle klientprogramvara 8.1.7 och senare

* Installera den lokala datagatewayen. [Anslut till lokala data från Logic Apps](../logic-apps/logic-apps-gateway-connection.md) listar stegen. Gatewayen krävs för att ansluta till en lokal Oracle Database eller en virtuell Azure-dator med Oracle DB installerat. 

    > [!NOTE]
    > Den lokala datagatewayen fungerar som en brygga och ger en säker data överföring mellan lokala data (data som inte finns i molnet) och dina Logic Apps. Samma Gateway kan användas med flera tjänster och flera data källor. Så du kanske bara behöver installera gatewayen en gång.

* Installera Oracle-klienten på den dator där du installerade den lokala datagatewayen. Se till att installera 64-bitars Oracle Data Provider för .NET från Oracle:  

  [64-bitars ODAC 12c version 4 (12.1.0.2.4) för Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Om Oracle-klienten inte är installerad uppstår ett fel när du försöker skapa eller använda anslutningen. Se vanliga fel i den här artikeln.


## <a name="add-the-connector"></a>Lägg till anslutningen

> [!IMPORTANT]
> Det finns inga utlösare för den här anslutningen. Den har bara åtgärder. När du skapar din Logic-app lägger du till en annan utlösare för att starta din Logi Kap par, till exempel **schema-upprepning** eller **begär ande/svars svar**. 

1. Skapa en tom Logic-app i [Azure Portal](https://portal.azure.com).

2. I början av din Logic app väljer du utlösaren **begäran/svar-begäran** : 

    ![En dialog ruta innehåller en ruta för att söka i alla utlösare. Det finns också en enda utlösare som visas, "Request/Response-Request", med en urvals knapp.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Välj **Spara**. När du sparar skapas en URL för begäran automatiskt. 

4. Välj **Nytt steg** och välj sedan **Lägg till en åtgärd**. Skriv in `oracle` för att se tillgängliga åtgärder: 

    ![En sökruta innehåller "Oracle". Sökningen skapar en träff med etiketten "Oracle Database". Det finns en tabbad sida som visar "utlösare (0)", en annan som visar "åtgärder (6)". Sex åtgärder visas. Den första av dessa är "Hämta rad förhands granskning".](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Detta är också det snabbaste sättet att se utlösare och åtgärder som är tillgängliga för alla anslutningar. Ange en del av kopplings namnet, till exempel `oracle` . Designern visar alla utlösare och åtgärder. 

5. Välj en av åtgärderna, till exempel **Oracle Database-get-rad**. Välj **Anslut via lokal datagateway**. Ange Oracle-servernamn, autentiseringsmetod, användar namn, lösen ord och välj gatewayen:

    ![Dialog rutan heter "Oracle Database-get Row". Det finns en ruta, markerad, märkt "Anslut via lokal datagateway". Nedan visas de fem andra text rutorna.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. När du är ansluten väljer du en tabell i listan och anger rad-ID: t i tabellen. Du måste känna till identifieraren för tabellen. Om du inte vet kan du kontakta Oracle DB-administratören och hämta utdata från `select * from yourTableName` . Det ger dig den information du behöver för att kunna fortsätta.

    I följande exempel returneras jobb data från en personal databas: 

    ![Dialog rutan "Hämta rad (förhands granskning)" har två text rutor: "Tabell namn", som innehåller "H R JOBS" och har en listruta, och "rad i d", som innehåller "S A A REP".](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. I det här nästa steg kan du använda någon av de andra anslutningarna för att bygga ditt arbets flöde. Om du vill testa att hämta data från Oracle skickar du ett e-postmeddelande med Oracle-data med hjälp av någon av e-postanslutningarna, till exempel Office 365 Outlook. Använd de dynamiska tokens från Oracle-tabellen för att bygga `Subject` och `Body` för din e-post:

    ![Det finns två dialog rutor. Rutorna "Skicka ett e-postmeddelande" innehåller rutor för att ange "Body", "subject" och "till"-adressen för e-postmeddelandet. I dialog rutan Lägg till dynamiskt innehåll kan du söka efter dynamiskt innehåll från appar och tjänster i flödet.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Spara** din Logic app och välj sedan **Kör**. Stäng designern och titta på körnings historiken för status. Om det Miss lyckas väljer du raden fel meddelande. Designern öppnas och visar det steg som misslyckades, och visar även fel informationen. Om det lyckas bör du få ett e-postmeddelande med den information som du har lagt till.


### <a name="workflow-ideas"></a>Arbets flödes idéer

* Du vill övervaka #oracle hashtagg och placera tweets i en databas så att de kan frågas och användas i andra program. I en Logic-app lägger du till `Twitter - When a new tweet is posted` utlösaren och anger **#oracle** hashtagg. Lägg sedan till `Oracle Database - Insert row` åtgärden och välj din tabell:

    ![I dialog rutan "när en ny tweet har publicerats" visas "hashtagg Oracle" som Sök text och du kan ange kontroll frekvens. Den här dialog rutan leder till dialog rutan Oracle Database som låter dig välja åtgärden.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Meddelanden skickas till en Service Bus kö. Du vill hämta dessa meddelanden och placera dem i en databas. I en Logic-app lägger du till `Service Bus - when a message is received in a queue` utlösaren och väljer kön. Lägg sedan till `Oracle Database - Insert row` åtgärden och välj din tabell:

    ![Meddelandet "när ett meddelande tas emot..." i dialog rutan visas "beställningar" som "könamn" och du kan ange kontroll frekvens. Den här rutan leder till dialog rutan "Infoga rad (förhands granskning)" som låter dig välja "Tabell namn".](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Vanliga fel

#### <a name="error-cannot-reach-the-gateway"></a>**Fel**: det går inte att ansluta till gatewayen

**Orsak**: den lokala datagatewayen kan inte ansluta till molnet. 

**Minskning**: kontrol lera att din gateway körs på den lokala datorn där du installerade den och att den kan ansluta till Internet.  Vi rekommenderar att du inte installerar gatewayen på en dator som kan vara avstängd eller i vilo läge. Du kan också starta om den lokala datagateway-tjänsten (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fel**: providern som används är föråldrad: system. data. OracleClient kräver version 8.1.7 av Oracle-klientprogramvaran eller mer. Se [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) så här installerar du den officiella providern.

**Orsak**: Oracle-klientens SDK är inte installerad på den dator där den lokala datagatewayen körs.  

**Lösning**: Hämta och installera Oracle client SDK på samma dator som den lokala datagatewayen.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fel**: tabellen [TableName] definierar inga nyckel kolumner

**Orsak**: tabellen har ingen primär nyckel.  

**Lösning**: Oracle Database-anslutningen kräver att en tabell med en primär nyckel kolumn används.
 
## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/oracle/). 

## <a name="get-some-help"></a>Få hjälp

[Sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html) är en bra plats för att ställa frågor, besvara frågor och se vad andra Logic Apps användare gör. 

Du kan hjälpa till att förbättra Logic Apps och anslutningar genom att rösta och skicka dina idéer till [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) . 


## <a name="next-steps"></a>Nästa steg
[Skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)och utforska de tillgängliga anslutningarna i Logic Apps i [API-listan](apis-list.md).