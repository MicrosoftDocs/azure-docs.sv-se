---
title: Hantera brand Väggs regler – Azure CLI – Azure Database for MariaDB
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MariaDB brand Väggs regler med hjälp av kommando raden i Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef04e2b4c820c14ea7df6c35ecb0189ef31ef7dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665046"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure CLI
Brand Väggs regler på server nivå kan användas för att hantera åtkomst till en Azure Database for MariaDB-Server från en speciell IP-adress eller ett intervall med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, Visa och Visa brand Väggs regler för hantering av servern. En översikt över Azure Database for MariaDB brand väggar finns i [Azure Database for MariaDB Server brand Väggs regler](./concepts-firewall-rules.md).

Virtual Network-regler (VNet) kan också användas för att skydda åtkomsten till servern. Lär dig mer om [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure CLI](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Förutsättningar
* [Installera Azure CLI](/cli/azure/install-azure-cli).
* En [Azure Database for MariaDB Server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Kommandon för brand Väggs regel:
Kommandot **AZ MariaDB Server Firewall-Rule** används från Azure CLI för att skapa, ta bort, lista, Visa och uppdatera brand Väggs regler.

Kommandon:
- **skapa**: skapa en brand Väggs regel för Azure MariaDB Server.
- **ta bort**: ta bort en regel för Azure MariaDB Server-brandvägg.
- **lista**: visar en lista över brand Väggs regler för Azure MariaDB Server.
- **Visa**: Visa information om en brand Väggs regel för Azure MariaDB Server.
- **uppdatering**: uppdatera en brand Väggs regel för Azure MariaDB Server.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Logga in på Azure och lista dina Azure Database for MariaDB-servrar
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

5. Visa en lista över Azure-databaser för MariaDB-servrar för din prenumeration och resurs grupp om du är osäker på namnen. Använd kommandot [AZ MariaDB Server List](/cli/azure/mariadb/server#az-mariadb-server-list) .

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Anteckna attributet name i listan, som du måste ange den MariaDB-server som du vill arbeta med. Om det behövs bekräftar du informationen för servern och använder attributet name för att kontrol lera att den är korrekt. Använd kommandot [AZ MariaDB server show](/cli/azure/mariadb/server#az-mariadb-server-show) .

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Visa en lista med brand Väggs regler på Azure Database for MariaDB Server 
Använd Server namnet och resurs grupps namnet och ange de befintliga reglerna för Server brand väggen på servern. Använd kommandot [AZ MariaDB Server Firewall List](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) .  Observera att attributet Server namn anges i växeln **--Server** och inte i växeln **--Name** . 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
I utdata visas reglerna, om de finns, i JSON-format (som standard). Du kan använda växeln **--output Table** för att skriva ut resultaten i ett mer läsbart tabell format.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Skapa en brand Väggs regel på Azure Database for MariaDB Server
Skapa en ny brand Väggs regel på servern med hjälp av namnet på Azure MariaDB-servern och resurs gruppens namn. Använd kommandot [AZ MariaDB Server Firewall Create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) . Ange ett namn för regeln, samt Start-IP och slut-IP (för att ge åtkomst till ett intervall med IP-adresser) för regeln.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enskild IP-adress anger du samma IP-adress som Start-IP och slut-IP, som i det här exemplet.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure Database for MariaDB-Server, anger du IP-adressen 0.0.0.0 som Start-IP och slut-IP, som i det här exemplet.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När det är klart visar varje utdata-kommando information om brand Väggs regeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Uppdatera en brand Väggs regel på Azure Database for MariaDB Server 
Använd namnet på Azure MariaDB-servern och resurs grupps namnet och uppdatera en befintlig brand Väggs regel på servern. Använd kommandot [AZ MariaDB Server Firewall Update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) . Ange namnet på den befintliga brand Väggs regeln som ininformation, samt de Start-IP-och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När kommandot har slutförts visas information om den brand Väggs regel som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

> [!NOTE]
> Om brand Väggs regeln inte finns skapas regeln av kommandot Update.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Visa information om brand Väggs regler på Azure Database for MariaDB Server
Med hjälp av namnet på Azure MariaDB-servern och namnet på resurs gruppen visar du den befintliga brand Väggs regel informationen från servern. Använd kommandot [AZ MariaDB Server Firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) . Ange namnet på den befintliga brand Väggs regeln som inmatade.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När kommandot har slutförts visas information om den brand Väggs regel som du har angett i JSON-format (som standard). Om det uppstår ett fel visar utdata i stället fel meddelande text i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Ta bort en brand Väggs regel på Azure Database for MariaDB Server
Ta bort en befintlig brand Väggs regel från servern med hjälp av namnet på Azure MariaDB-servern och resurs gruppens namn. Använd kommandot [AZ MariaDB Server Firewall Delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) . Ange namnet på den befintliga brand Väggs regeln.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När det är klart finns det inga utdata. Vid fel visas fel meddelande text.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [brand Väggs regler för Azure Database for MariaDB Server](./concepts-firewall-rules.md).
- [Skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-portal.md).
- Mer säker åtkomst till servern genom [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure CLI](howto-manage-vnet-cli.md).