---
title: Hantera brandväggsregler – Azure CLI – Azure Database for MariaDB
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MariaDB brandväggsregler med hjälp av Azure CLI-kommandoraden.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 87ff75a07bd1b91121d614e0f41c0ecf216e1b41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791723"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure Database for MariaDB brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure Database for MariaDB Server från en specifik IP-adress eller ett INTERVALL med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista och visa brandväggsregler för att hantera servern. En översikt över hur Azure Database for MariaDB brandväggar finns i [Azure Database for MariaDB serverbrandväggsregler.](./concepts-firewall-rules.md)

Virtual Network (VNet)-regler kan också användas för att skydda åtkomsten till servern. Lär dig mer [om att skapa Virtual Network hantera tjänstslutpunkter och regler med hjälp av Azure CLI.](howto-manage-vnet-cli.md)

## <a name="prerequisites"></a>Förutsättningar
* [Installera Azure CLI](/cli/azure/install-azure-cli).
* En [Azure Database for MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Kommandon för brandväggsregler:
Kommandot **az mariadb server firewall-rule** används från Azure CLI för att skapa, ta bort, lista, visa och uppdatera brandväggsregler.

Kommandon:
- **skapa:** Skapa en brandväggsregel för Azure MariaDB-server.
- **delete**: Ta bort en brandväggsregel för Azure MariaDB-server.
- **list**: Lista Brandväggsregler för Azure MariaDB-server.
- **show**: Visa information om en Azure MariaDB-serverbrandväggsregel.
- **uppdatera**: Uppdatera en brandväggsregel för Azure MariaDB-server.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Logga in på Azure och lista dina Azure Database for MariaDB servrar
Anslut Azure CLI säkert med ditt Azure-konto med hjälp av **kommandot az login.**

1. Kör följande kommando från kommandoraden:
   ```azurecli
   az login
   ```
   Det här kommandot matar ut en kod som ska användas i nästa steg.

2. Använd en webbläsare för att öppna [https://aka.ms/devicelogin](https://aka.ms/devicelogin) sidan och ange sedan koden.

3. Logga in med dina autentiseringsuppgifter för Azure när du uppmanas till det.

4. När din inloggning har auktoriserats skrivs en lista över prenumerationer ut i konsolen. Kopiera ID:t för den önskade prenumerationen för att ange att den aktuella prenumerationen ska användas. Använd kommandot [az account set.](/cli/azure/account#az_account_set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Lista Azure Databases for MariaDB-servrarna för din prenumeration och resursgrupp om du är osäker på namnen. Använd kommandot [az mariadb server list.](/cli/azure/mariadb/server#az_mariadb_server_list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Observera namnattributet i listan, som du måste ange att MariaDB-servern ska fungera med. Om det behövs bekräftar du informationen för servern och använder namnattributet för att kontrollera att den är korrekt. Använd kommandot [az mariadb server show.](/cli/azure/mariadb/server#az_mariadb_server_show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista brandväggsregler på Azure Database for MariaDB Server 
Med hjälp av servernamnet och resursgruppens namn listar du de befintliga serverbrandväggsreglerna på servern. Använd kommandot [az mariadb server firewall list.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_list)  Observera att servernamnattributet anges i **växeln --server** och inte i **växeln --name.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar reglerna, om de finns, i JSON-format (som standard). Du kan använda **växeln --output table** för att mata ut resultatet i ett mer läsbart tabellformat.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Skapa en brandväggsregel på Azure Database for MariaDB Server
Skapa en ny brandväggsregel på servern med hjälp av Azure MariaDB-servernamnet och resursgruppens namn. Använd kommandot [az mariadb server firewall create.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) Ange ett namn för regeln, samt start-IP och slut-IP (för att ge åtkomst till ett intervall med IP-adresser) för regeln.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enskild IP-adress anger du samma IP-adress som Start-IP och Slut-IP, som i det här exemplet.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure Database for MariaDB-server anger du IP-adressen 0.0.0.0 som Start-IP och Slut-IP, som i det här exemplet.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När kommandot har skapats visas information om brandväggsregeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel visar utdata felmeddelandetexten i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Uppdatera en brandväggsregel på Azure Database for MariaDB server 
Uppdatera en befintlig brandväggsregel på servern med hjälp av Azure MariaDB-servernamnet och resursgruppens namn. Använd kommandot [az mariadb server firewall update.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_update) Ange namnet på den befintliga brandväggsregeln som indata, samt start-IP- och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När det har lyckats visar kommandot utdata information om brandväggsregeln som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel visar utdata felmeddelandetexten i stället.

> [!NOTE]
> Om brandväggsregeln inte finns skapas regeln av uppdateringskommandot.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Visa information om brandväggsregler på Azure Database for MariaDB Server
Använd Azure MariaDB-servernamnet och resursgruppens namn och visa information om befintliga brandväggsregler från servern. Använd kommandot [az mariadb server firewall show.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_show) Ange namnet på den befintliga brandväggsregeln som indata.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När det har lyckats visar kommandot utdata information om brandväggsregeln som du har angett i JSON-format (som standard). Om det uppstår ett fel visar utdata felmeddelandetexten i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Ta bort en brandväggsregel på Azure Database for MariaDB Server
Ta bort en befintlig brandväggsregel från servern med hjälp av Azure MariaDB-servernamnet och resursgruppens namn. Använd kommandot [az mariadb server firewall delete.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_delete) Ange namnet på den befintliga brandväggsregeln.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När det har lyckats finns det inga utdata. Vid fel visas felmeddelandetext.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Database for MariaDB Server-brandväggsregler.](./concepts-firewall-rules.md)
- [Skapa och hantera Azure Database for MariaDB brandväggsregler med hjälp av Azure Portal](./howto-manage-firewall-portal.md).
- Ytterligare säker åtkomst till servern genom att [skapa och hantera Virtual Network tjänstslutpunkter och regler med hjälp av Azure CLI.](howto-manage-vnet-cli.md)