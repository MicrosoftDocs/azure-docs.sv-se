---
title: 'Snabb start: Anslut med Node.js-Azure Database for MySQL'
description: I den här snabbstarten finns flera kodexempel i Node.js som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/11/2020
ms.openlocfilehash: 6a9134e13e3145daea1eed81c4aa8795a0a49950
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588241"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: Använd Node.js för att ansluta och fråga efter data i Azure Database for MySQL

I den här snabb starten ansluter du till en Azure Database for MySQL med hjälp av Node.js. Sedan använder du SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac, Ubuntu Linux och Windows-plattformar. 

Det här avsnittet förutsätter att du är van att utveckla med Node.js, men du är inte nybörjare på att arbeta med Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En Azure Database for MySQL-server. [Skapa en Azure Database for MySQL-server med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) eller [skapa en Azure Database for MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Se till att den IP-adress som du ansluter från har lagts till i serverns brand Väggs regler med hjälp av [Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-nodejs-and-the-mysql-connector"></a>Installera Node.js och MySQL Connector

Beroende på din plattform följer du anvisningarna i lämpligt avsnitt för att installera [Node.js](https://nodejs.org). Använd NPM för att installera [MySQL](https://www.npmjs.com/package/mysql) -paketet och dess beroenden i projektmappen.

### <a name="windows"></a>Windows

1. Besök [hämtningssidan för Node.js](https://nodejs.org/en/download/) och välj sedan önskat alternativ för Windows installeringsverktyg.
2. Skapa en lokal projektmapp som till exempel `nodejsmysql`. 
3. Öppna kommando tolken och ändra katalogen till projektmappen, till exempel `cd c:\nodejsmysql\`
4. Kör NPM-verktyget för att installera mysql-biblioteket i projektmappen.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Verifiera installationen genom att kontrollera `npm list`-utdatatexten. Versionsnumret kan variera när nya korrigeringsfiler blir tillgängliga.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Ange följande kommandon för att installera **npm** och **Node.js**, en användarvänlig pakethanterare för Node.js.

   ```bash
    # Using Ubuntu
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Using Debian, as root
    curl -sL https://deb.nodesource.com/setup_14.x | bash -
    apt-get install -y nodejs
   ```

2. Kör följande kommandon för att skapa projektmappen `mysqlnodejs` och installera mysql-paketet i den mappen.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Verifiera installationen genom att kontrollera npm-listans utdatatext. Versionsnumret kan variera när nya korrigeringsfiler blir tillgängliga.

### <a name="macos"></a>macOS

1. Besök [ sidanNode.js hämtningar](https://nodejs.org/en/download/)och välj sedan installations programmet för MacOS.

2. Kör följande kommandon för att skapa projektmappen `mysqlnodejs` och installera mysql-paketet i den mappen.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Verifiera installationen genom att kontrollera `npm list`-utdatatexten. Versionsnumret kan variera när nya korrigeringsfiler blir tillgängliga.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal väljer du **alla resurser** och söker sedan efter den server som du har skapat (till exempel **mydemoserver**).
3. Välj servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="Azure Database för MySQL-servernamn":::

## <a name="running-the-javascript-code-in-nodejs"></a>Kör JavaScript-kod i Node.js

1. Klistra in JavaScript-koden i en textfil och spara sedan filen till en projektmapp med filnamnstillägget .js (till exempel C:\nodejsmysql\createtable.js eller /home/username/nodejsmysql/createtable.js).
2. Öppna kommando tolken eller bash-gränssnittet och ändra sedan katalogen i projektmappen `cd nodejsmysql` .
3. Kör programmet genom att ange Node-kommandot följt av fil namnet, till exempel `node createtable.js` .
4. I Windows, om node-programmet inte finns på sökvägen för miljövariabeln, kan du behöva använda den fullständiga sökvägen för att starta nodprogrammet, till exempel `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data

Använd följande kod för att ansluta och läsa in data med hjälp av SQL-instruktionerna **CREATE TABLE** och **INSERT INTO**.

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Funktionen [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Funktionen [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. 

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }
});

function queryDatabase(){
    conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
        if (err) throw err; 
        console.log('Dropped inventory table if existed.');
    })
        conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Created inventory table.');
    })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
        else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
    function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.end(function (err) { 
    if (err) throw err;
    else  console.log('Done.') 
    });
};
```

## <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. 

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Metoden [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Metoden [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. Resultatmatrisen används för resultat från frågan.

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }
    });

function readData(){
    conn.query('SELECT * FROM inventory', 
        function (err, results, fields) {
            if (err) throw err;
            else console.log('Selected ' + results.length + ' row(s).');
            for (i = 0; i < results.length; i++) {
                console.log('Row: ' + JSON.stringify(results[i]));
            }
            console.log('Done.');
        })
    conn.end(
        function (err) { 
            if (err) throw err;
            else  console.log('Closing connection.') 
    });
};
```

## <a name="update-data"></a>Uppdatera data

Använd följande kod för att ansluta och läsa data med SQL-instruktionen **UPDATE**. 

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Metoden [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Metoden [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. 

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Ta bort data

Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion. 

Metoden [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) används som gränssnitt med MySQL-servern. Metoden [connect()](https://github.com/mysqljs/mysql#establishing-connections) används för att etablera anslutningen till servern. Metoden [query()](https://github.com/mysqljs/mysql#performing-queries) används för att köra SQL-frågor mot en MySQL-databas. 

Ersätt parametrarna `host`, `user`, `password` och `database` med de värden som du angav när du skapade servern och databasen.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./concepts-migrate-import-export.md)
