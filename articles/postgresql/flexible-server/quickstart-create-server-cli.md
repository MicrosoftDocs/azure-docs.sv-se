---
title: 'Snabbstart: Skapa en server – Azure CLI – Azure Database for PostgreSQL – flexibel server'
description: Den här snabbstarten beskriver hur du använder Azure CLI för att skapa Azure Database for PostgreSQL flexibel server i en Azure-resursgrupp.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 320457365a36825564154c36ad843ef665fc2d2c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791561"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Snabbstart: Skapa en Azure Database for PostgreSQL flexibel server med Azure CLI

Den här snabbstarten visar hur du använder [Azure CLI-kommandon](/cli/azure/get-started-with-azure-cli) [i Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for PostgreSQL flexibel server på fem minuter. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT] 
> Azure Database for PostgreSQL flexibel server är för närvarande i förhandsversion.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Det [Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnadsfritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kodblocken, klistra in det i Cloud Shell och välj Retur **för** att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) Observera **id-egenskapen,** som refererar till **prenumerations-ID för** ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den specifika prenumerationen under ditt konto med [kommandot az account](/cli/azure/account#az_account_set) set. Anteckna ID-värdet **från az** login-utdata som ska användas som värde för  **prenumerationsargumentet** i kommandot . Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta alla dina prenumerationer använder [du az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Skapa en flexibel server

Skapa en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) med `az group create` kommandot och skapa sedan din flexibla PostgreSQL-server i den här resursgruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Skapa en flexibel server med `az postgres flexible-server create` kommandot . En server kan innehålla flera databaser. Följande kommando skapar en server med hjälp av tjänstens standardvärden och värden från den lokala Azure [CLI-kontexten](/cli/azure/local-context): 

```azurecli
az postgres flexible-server create
```

Servern som skapas har attributen nedan: 
- Automatiskt genererat servernamn, administratörsnamn, administratörslösenord, resursgruppsnamn (om det inte redan har angetts i lokal kontext) och på samma plats som resursgruppen 
- Tjänstens standardinställningar för återstående serverkonfigurationer: beräkningsnivå (Generell användning), beräkningsstorlek/SKU (D2s_v3 – 2 virtuella kärnor, 8 GB RAM-minne), kvarhållningsperiod för säkerhetskopior (7 dagar) och PostgreSQL-version (12)
- Standardanslutningsmetoden är Privat åtkomst (VNet-integrering) med ett automatiskt genererat virtuellt nätverk och undernät

> [!NOTE] 
> Anslutningsmetoden kan inte ändras när du har skapat servern. Om du till exempel valde *Privat åtkomst (VNet-integrering)* under skapa kan du inte ändra till Offentlig *åtkomst (tillåtna IP-adresser)* efter att du skapat. Vi rekommenderar starkt att du skapar en server med privat åtkomst för säker åtkomst till servern med VNet-integrering. Läs mer om privat åtkomst i [begreppsartikeln](./concepts-networking.md).

Om du vill ändra standardvärdena kan du läsa referensdokumentationen för Azure CLI <!--FIXME --> för en fullständig lista över konfigurerbara CLI-parametrar. 

> [!NOTE]
> Anslutningar till Azure Database for PostgreSQL kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 5432 inte tillåts. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 5432.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Ansluta med postgreSQL-kommandoradsklienten

Eftersom den flexibla servern skapades med privat åtkomst *(VNet-integrering)* måste du ansluta till servern från en resurs inom samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i det virtuella nätverk som skapats. 

När den virtuella datorn har skapats kan du använda SSH i datorn och installera **[psql-kommandoradsverktyget.](https://www.postgresql.org/download/)**

Anslut med psql med kommandot nedan. Ersätt värden med det faktiska servernamnet och lösenordet. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du bara vill ta bort den nyligen skapade servern kan du köra `az postgres flexible-server delete` kommandot .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Distribuera en Django-app med App Service och PostgreSQL](tutorial-django-app-service-postgres.md)
