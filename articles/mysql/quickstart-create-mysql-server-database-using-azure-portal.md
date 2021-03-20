---
title: 'Snabb start: skapa en server-Azure Portal-Azure Database for MySQL'
description: Den här artikeln beskriver hur du använder Azure Portal för att skapa ett exempel på en Azure-databas för MySQL-server på fem minuter.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: b8f3a8e06bb27051417205c4dc3141948960bb00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567804"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Snabb start: skapa en Azure Database for MySQL-server med hjälp av Azure Portal

Azure Database for MySQL är en hanterad tjänst som du använder för att köra, hantera och skala hög tillgängliga MySQL-databaser i molnet. Den här snabb starten visar hur du använder Azure Portal för att skapa en Azure Database for MySQL enskild server. Det visar också hur du ansluter till servern.

## <a name="prerequisites"></a>Förutsättningar
En Azure-prenumeration krävs. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Skapa en Azure Database for MySQL enskild server
1. Gå till [Azure Portal](https://portal.azure.com/) om du vill skapa en MySQL-databas för enskild server. Sök efter och välj **Azure Database for MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Hitta Azure Database for MySQL":::

1. Välj **Lägg till**.

2. På sidan **välj Azure Database for MySQL distributions alternativ** väljer du  **enskild server**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Skärm bild som visar alternativet enskild server.":::

3. Ange grundläggande inställningar för en ny enskild server:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Skärm bild som visar sidan Skapa MySQL-server.":::

   **Inställning** | **Föreslaget värde** | **Beskrivning**
   ---|---|---
   Prenumeration | Din prenumeration | Välj önskad Azure-prenumeration.
   Resursgrupp | **myresourcegroup** | Ange en ny resurs grupp eller en befintlig resurs grupp från din prenumeration.
   Servernamn | **mydemoserver** | Ange ett unikt namn. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
   Datakälla |**Ingen** | Välj **Ingen** om du vill skapa en ny server från grunden. Välj endast **säkerhets kopiering** om du återställer från en geo-säkerhetskopia av en befintlig server.
   Location |Önskad plats | Välj en plats i listan.
   Version | Senaste huvudversion| Använd den senaste huvud versionen. Se [alla versioner som stöds](concepts-supported-versions.md).
   Beräkning och lagring | Använd standardinställningarna| Standard pris nivån är **generell användning** med **4 virtuella kärnor** och **100 GB** lagring. Kvarhållning av säkerhets kopior har angetts till **7 dagar**, med alternativet **geografiskt redundant** säkerhets kopiering.<br/>Gå igenom sidan med [priser](https://azure.microsoft.com/pricing/details/mysql/) och uppdatera standardinställningarna om du behöver.
   Användarnamn för administratör | **mydemoadmin** | Ange användar namnet för Server administratören. Du kan inte använda **azure_superuser**, **administratör**, **administratör**, **rot**, **gäst** eller **offentlig** för administratörs användar namnet.
   Lösenord | Ett lösenord | Ett nytt lösen ord för Server administratörs användaren. Lösen ordet måste vara mellan 8 och 128 tecken långt och innehålla en kombination av versaler eller gemener, siffror och icke-alfanumeriska tecken (!, $, #,% osv.).
  

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapats på grund pris nivån inte kan skalas senare till Generell användning eller Minnesoptimerade.

4. Välj **Granska + skapa** för att etablera servern.

5. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. Välj **gå till resurs** för att gå till den nyss skapade Server sidan:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Skärm bild som visar meddelandet distributionen är klar.":::

[Har du problem? Berätta för oss.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Som standard skyddas den nya servern med en brand vägg. För att ansluta måste du ge åtkomst till din IP-adress genom att utföra följande steg:

1. Gå till **anslutnings säkerhet** i den vänstra rutan för Server resursen. Om du inte vet hur du hittar din resurs, se [så här öppnar du en resurs](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Skärm bild som visar sidan anslutnings säkerhets > brand Väggs regler.":::

2. Välj **Lägg till aktuell klient-IP-adress** och välj sedan **Spara**.

   > [!NOTE]
   > Undvik anslutnings problem genom att kontrol lera om nätverket tillåter utgående trafik över Port 3306, som används av Azure Database for MySQL. 

Du kan lägga till fler IP-adresser eller ange ett IP-intervall för att ansluta till servern från de IP-adresserna. Mer information finns i [Hantera brand Väggs regler på en Azure Database for MySQL-server](./concepts-firewall-rules.md).


[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Anslut till servern med hjälp av mysql.exe
Du kan använda antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från den lokala miljön. I den här snabb starten använder vi mysql.exe i [Azure Cloud Shell](../cloud-shell/overview.md) för att ansluta till servern.


1. Öppna Azure Cloud Shell i portalen genom att välja den första knappen i verktygsfältet, som du ser i följande skärm bild. Anteckna Server namnet, Server administratörs namnet och prenumerationen för din nya server i **översikts** avsnittet, som visas på skärm bilden.

    > [!NOTE]
    > Om du öppnar Cloud Shell för första gången uppmanas du att skapa en resurs grupp och ett lagrings konto. Det här är en engångs åtgärd. Den kommer automatiskt att bifogas för alla sessioner.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Skärm bild som visar Cloud Shell i Azure Portal.":::
2. Kör följande kommando i Azure Cloud Shell terminalen. Ersätt värdena som visas här med det faktiska Server namnet och administratörens användar namn. För Azure Database for MySQL måste du lägga till i `@\<servername>` Administratörs användar namnet, som du ser här: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Så här ser det ut i Cloud Shell terminalen:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. I samma Azure Cloud Shell terminal skapar du en databas med namnet `guest` :
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Växla till `guest` databasen:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Ange `quit` och välj sedan **RETUR** för att avsluta MySQL.

[Har du problem? Berätta för oss.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Rensa resurser
Nu har du skapat en Azure Database for MySQL-server i en resurs grupp.  Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort resurs gruppen, eller så kan du bara ta bort MySQL-servern. Slutför följande steg för att ta bort resurs gruppen:
1. I Azure Portal söker du efter och väljer **resurs grupper**.
2. I listan över resurs grupper väljer du namnet på din resurs grupp.
3. På **översikts** sidan för resurs gruppen väljer du **ta bort resurs grupp**.
4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

Om du vill ta bort servern kan du välja **ta bort** på sidan **Översikt** för servern, som du ser här:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Skärm bild som visar knappen Ta bort på sidan Server översikt.":::

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
>[Bygg en PHP-app i Windows med MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Bygga PHP-app på Linux med MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/mysql-doc-feedback)
