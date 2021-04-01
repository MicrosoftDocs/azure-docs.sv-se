---
title: Hantera brand Väggs regler – Azure CLI – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du skapar och hanterar brand Väggs regler i Azure Database for PostgreSQL-enskild server med hjälp av kommando raden i Azure CLI.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422747"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Skapa och hantera brand Väggs regler i Azure Database for PostgreSQL-enskild server med Azure CLI
Brand Väggs regler på server nivå kan användas för att hantera åtkomst till en Azure Database for PostgreSQL-Server från en speciell IP-adress eller ett intervall med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, Visa och Visa brand Väggs regler för hantering av servern. En översikt över Azure Database for PostgreSQL brand Väggs regler finns i [Azure Database for postgresql server brand Väggs regler](concepts-firewall-rules.md).

Virtual Network-regler (VNet) kan också användas för att skydda åtkomsten till servern. Lär dig mer om [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- Installera kommando rads verktyget för [Azure CLI](/cli/azure/install-azure-cli) eller Använd Azure Cloud Shell i webbläsaren.
- En [Azure Database for postgresql server och databas](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurera brand Väggs regler för Azure Database for PostgreSQL
[AZ postgres Server Firewall – Rule](/cli/azure/postgres/server/firewall-rule) commands används för att konfigurera brand Väggs regler.

## <a name="list-firewall-rules"></a>Visa lista över brand Väggs regler 
Om du vill visa en lista över de befintliga reglerna för brand vägg för server kör du kommandot [AZ postgres Server Firewall-Rule List](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
I utdata visas brand Väggs reglerna som standard i JSON-format. Du kan använda växeln `--output table` för att få ett mer läsbart tabell format som utdata.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Skapa brandväggsregel
Om du vill skapa en ny brand Väggs regel på servern kör du kommandot [AZ postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) . 


Om du vill tillåta åtkomst till en enskild IP-adress anger du samma adress i `--start-ip-address` och `--end-ip-address` , som i det här exemplet ersätter du IP-adressen som visas här med din speciella IP-adress.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure Database for PostgreSQL-Server, anger du IP-adressen 0.0.0.0 som Start-IP och slut-IP, som i det här exemplet.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När kommandot har slutförts visas information om den brand Väggs regel som du har skapat som standard i JSON-format. Om det uppstår ett fel visar utdata ett fel meddelande i stället.

## <a name="update-firewall-rule"></a>Uppdatera brand Väggs regel 
Uppdatera en befintlig brand Väggs regel på servern med hjälp av kommandot [AZ postgres Server Firewall-Rule Update](/cli/azure/postgres/server/firewall-rule) . Ange namnet på den befintliga brand Väggs regeln som inmatade och de Start-IP-och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
När kommandot har slutförts visas information om den brand Väggs regel som du har uppdaterat som standard i JSON-format. Om det uppstår ett fel visar utdata ett fel meddelande i stället.
> [!NOTE]
> Om brand Väggs regeln inte finns skapas den av kommandot Update.

## <a name="show-firewall-rule-details"></a>Visa information om brand Väggs regler
Du kan också visa information om en befintlig brand Väggs regel på server nivå genom att köra [AZ postgres Server Firewall-Rule show](/cli/azure/postgres/server/firewall-rule) kommandot.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
När kommandot har slutförts visas information om den brand Väggs regel som du har angett som standard i JSON-format. Om det uppstår ett fel visar utdata ett fel meddelande i stället.

## <a name="delete-firewall-rule"></a>Ta bort brand Väggs regel
Om du vill återkalla åtkomsten för ett IP-intervall till servern tar du bort en befintlig brand Väggs regel genom att köra kommandot [AZ postgres Server Firewall-Rule Delete](/cli/azure/postgres/server/firewall-rule) . Ange namnet på den befintliga brand Väggs regeln.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
När det är klart finns det inga utdata. Vid fel returneras fel meddelande texten.

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du använda en webbläsare för att [skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure Portal](howto-manage-firewall-using-portal.md).
- Lär dig mer om [brand Väggs regler för Azure Database for postgresql server](concepts-firewall-rules.md).
- Mer säker åtkomst till servern genom [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).
- Information om hur du ansluter till en Azure Database for PostgreSQL-Server finns i [anslutnings bibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
