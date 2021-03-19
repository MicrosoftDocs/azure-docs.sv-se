---
title: 'Snabb start: Anslut – MySQL Workbench – Azure Database for MySQL – flexibel Server'
description: Den här snabb starten visar hur du använder MySQL Workbench för att ansluta och fråga efter data från Azure Database for MySQL-flexibel Server.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: d604ca8c8979ec98b990f8002ce29d0df92ac2af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90948518"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Snabb start: Använd MySQL Workbench för att ansluta och fråga efter data i Azure Database for MySQL-flexibel Server (för hands version)


> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här snabb starten visar hur du ansluter till en Azure Database for MySQL flexibel server med hjälp av MySQL Workbench-programmet.

## <a name="prerequisites"></a>Förutsättningar

I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:

- [Skapa en Azure Database for MySQL flexibel server med Azure Portal](./quickstart-create-server-portal.md)
- [Skapa en Azure Database for MySQL flexibel server med Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Förbereda klient arbets stationen
- Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server. Se [skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).
- Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern. Se [skapa och hantera Azure Database for MySQL flexibla Server brand Väggs regler med hjälp av Azure CLI](./how-to-manage-firewall-cli.md).

- Hämta och installera MySQL Workbench på datorn från [webbplatsen för MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta anslutnings informationen som behövs för att ansluta till den flexibla servern. Du behöver det fullständigt kvalificerade Server namnet och inloggnings uppgifterna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal väljer du **alla resurser** och söker sedan efter den server som du har skapat (till exempel **mydemoserver**).
3. Välj servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Ansluta till servern med MySQL Workbench

Så här ansluter du till Azure Database for MySQL flexibel server med MySQL Workbench:

1. Starta programmet MySQL Workbench på datorn.

2. I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="konfigurera ny anslutning":::

    | **Parametrar** | **Föreslaget värde** | **Fältbeskrivning** |
    |---|---|---|
    |    Anslutningsnamn | Demoanslutning | Ange ett namn på anslutningen. |
    | Anslutningsmetod | Standard (TCP/IP) | Standard (TCP/IP) är tillräckligt. |
    | Värdnamn | *Server namn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för MySQL. Vår exempelserver visas som mydemoserver.mysql.database.azure.com. Använd det fullständiga domännamnet (\*.mysql.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern.  |
    | Port | 3306 | Använd alltid port 3306 när du ansluter till Azure Database för MySQL. |
    | Användarnamn |  *inloggnings namn för Server administratör* | Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för MySQL. Vårt exempel användar namn är min administratör. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet.
    | Lösenord | ditt lösenord | Klicka på **lagra i valv...** för att spara lösen ordet. |

3. Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade.

4. Spara anslutningen genom att klicka på **OK** .

5. I listan **MySQL-anslutningar** klickar du på panelen som motsvarar din server och väntar sedan tills anslutningen har upprättats.

    En ny SQL-flik öppnas med en tom redigerare där du kan ange dina frågor.

> [!NOTE]
> Krypterad anslutning med TLS 1,2 krävs och tillämpas på den Azure Database for MySQL flexibla servern. Även om det krävs ingen ytterligare konfiguration med TLS/SSL-certifikat för att MySQL Workbench ska kunna ansluta till servern, rekommenderar vi att du binder TLS/SSL CA-certifiering med MySQL Workbench. Mer information finns i [ansluta med TLS/SSL](./how-to-connect-tls-ssl.md)

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Skapa en tabell, infoga data, läsa data, uppdatera data, ta bort data

1. Kopiera och klistra in SQL-exempelkoden på en tom SQL-flik för att visa exempeldata.

    Den här koden skapar en tom databas med namnet quickstartdb och skapar sedan en exempeltabell med namnet inventory. Den infogar några rader och läser sedan raderna. Den ändrar data med ett uppdateringsuttryck och läser raderna igen. Slutligen tar den bort en rad och läser sedan raderna igen.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Skärmbilden visar ett exempel på SQL-koden i SQL Workbench och utdata efter att den har körts.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Fliken SQL i MySQL Workbench för att köra SQL-exempelkod":::

2. Om du vill köra SQL-exempelkoden klickar du på blixtikonen i verktygsfältet på fliken **SQL-fil**.
3. Lägg märke till de tre resultaten med flikar i avsnittet med **rutnätet med resultat** mitt på sidan.
4. Lägg märke till listan **Output** (Utdata) längst ned på sidan. Status för varje kommando visas.

Nu har du anslutit till Azure Database for MySQL flexibel server med hjälp av MySQL Workbench och du har efterfrågat data med hjälp av SQL-språket.

## <a name="next-steps"></a>Nästa steg
- [Krypterad anslutning med Transport Layer Security (TLS 1,2) i Azure Database for MySQL-flexibel Server](./how-to-connect-tls-ssl.md).
- Lär dig mer om [nätverk i Azure Database for MySQL flexibel Server](./concepts-networking.md).
- [Skapa och hantera Azure Database for MySQL flexibla Server brand Väggs regler med hjälp av Azure Portal](./how-to-manage-firewall-portal.md).
- [Skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure Portal](./how-to-manage-virtual-network-portal.md).
