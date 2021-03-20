---
title: Hantera brand Väggs regler – Azure CLI – Azure Database for MySQL
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MySQL brand Väggs regler med hjälp av kommando raden i Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaddd8b2979b30251301ad041ea4b872c23d680b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541342"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI
Brand Väggs regler på server nivå kan användas för att hantera åtkomst till en Azure Database for MySQL-server från en speciell IP-adress eller ett intervall med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, Visa och Visa brand Väggs regler för hantering av servern. En översikt över Azure Database for MySQL brand väggar finns i [Azure Database for MySQL server brand Väggs regler](./concepts-firewall-rules.md).

Virtual Network-regler (VNet) kan också användas för att skydda åtkomsten till servern. Lär dig mer om [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Förutsättningar
* [Installera Azure CLI](/cli/azure/install-azure-cli).
* En [Azure Database for MySQL server och databas](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Kommandon för brand Väggs regel:
Kommandot **AZ MySQL server Firewall-Rule** används från Azure CLI för att skapa, ta bort, lista, Visa och uppdatera brand Väggs regler.

Kommandon:
- **skapa**: skapa en brand Väggs regel för Azure MySQL server.
- **ta bort**: ta bort en regel för Azure MySQL server-brandvägg.
- **lista**: visar en lista över Azure MySQL-serverns brand Väggs regler.
- **Visa**: Visa information om en regel för Azure MySQL server-brandvägg.
- **uppdatering**: uppdatera en brand Väggs regel för Azure MySQL server.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Logga in på Azure och lista dina Azure Database for MySQL-servrar
Anslut säkert Azure CLI med ditt Azure-konto med hjälp av kommandot **AZ login** .

1. Kör följande kommando från kommando raden:
    ```azurecli
    az login
    ```
   Det här kommandot matar ut en kod som ska användas i nästa steg.

2. Använd en webbläsare för att öppna sidan [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange sedan koden.

3. Logga in med dina Azure-autentiseringsuppgifter vid prompten.

4. När inloggningen har godkänts skrivs en lista över prenumerationer ut i-konsolen. Kopiera ID: t för den önskade prenumerationen för att ange den aktuella prenumerationen som ska användas. Använd kommandot [AZ Account set](/cli/azure/account#az-account-set) .
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Visa en lista över Azure-databaser för MySQL-servrar för din prenumeration och resurs grupp om du är osäker på namnen. Använd kommandot [AZ MySQL Server List](/cli/azure/mysql/server#az-mysql-server-list) .

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Anteckna attributet name i listan, som du måste ange den MySQL-server som ska fungera. Om det behövs bekräftar du informationen för servern och använder attributet name för att kontrol lera att den är korrekt. Använd kommandot [AZ MySQL server show](/cli/azure/mysql/server#az-mysql-server-show) .

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Visa en lista med brand Väggs regler på Azure Database for MySQL Server 
Använd Server namnet och resurs grupps namnet och ange de befintliga reglerna för Server brand väggen på servern. Använd kommandot [AZ MySQL server Firewall List](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) .  Observera att attributet Server namn anges i växeln **--Server** och inte i växeln **--Name** . 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
I utdata visas reglerna, om de finns, i JSON-format (som standard). Du kan använda växeln **--output Table** för att skriva ut resultaten i ett mer läsbart tabell format.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Skapa en brand Väggs regel på Azure Database for MySQL Server
Skapa en ny brand Väggs regel på servern med hjälp av Azure MySQL-servernamnet och resurs gruppens namn. Använd kommandot [AZ MySQL server Firewall Create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) . Ange ett namn för regeln, samt Start-IP och slut-IP (för att ge åtkomst till ett intervall med IP-adresser) för regeln.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enskild IP-adress anger du samma IP-adress som Start-IP och slut-IP, som i det här exemplet.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure Database for MySQL-server, anger du IP-adressen 0.0.0.0 som Start-IP och slut-IP, som i det här exemplet.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När det är klart visar varje utdata-kommando information om brand Väggs regeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Uppdatera en brand Väggs regel på Azure Database for MySQL Server 
Använd namnet på Azure MySQL-servern och resurs grupps namnet och uppdatera en befintlig brand Väggs regel på servern. Använd [uppdaterings kommandot AZ MySQL server Firewall](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) . Ange namnet på den befintliga brand Väggs regeln som ininformation, samt de Start-IP-och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När kommandot har slutförts visas information om den brand Väggs regel som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

> [!NOTE]
> Om brand Väggs regeln inte finns skapas regeln av kommandot Update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Visa information om brand Väggs regler på Azure Database for MySQL Server
Med namnet på Azure MySQL-servern och resurs grupps namnet visar du den befintliga brand Väggs regel informationen från servern. Använd kommandot [AZ MySQL server Firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) . Ange namnet på den befintliga brand Väggs regeln som inmatade.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När kommandot har slutförts visas information om den brand Väggs regel som du har angett i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Ta bort en brand Väggs regel på Azure Database for MySQL Server
Ta bort en befintlig brand Väggs regel från servern med hjälp av Azure MySQL-servernamnet och resurs gruppens namn. Använd kommandot [AZ MySQL server Firewall Delete](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) . Ange namnet på den befintliga brand Väggs regeln.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När det är klart finns det inga utdata. Vid fel visas fel meddelande text.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [brand Väggs regler för Azure Database for MySQL server](./concepts-firewall-rules.md).
- [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-using-portal.md).
- Mer säker åtkomst till servern genom [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).