---
title: 'Snabb start: skapa en server – Azure CLI-Azure Database for PostgreSQL-flexibel Server'
description: I den här snabb starten beskrivs hur du använder Azure CLI för att skapa en Azure Database for PostgreSQL flexibel server i en Azure-resurs grupp.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d43fab43b05ccfd88308390f58ba48ff9111b295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94842829"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Snabb start: skapa en Azure Database for PostgreSQL flexibel server med Azure CLI

Den här snabb starten visar hur du använder [Azure CLI](/cli/azure/get-started-with-azure-cli) -kommandon i [Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for PostgreSQL flexibel server på fem minuter. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT] 
> Azure Database for PostgreSQL flexibel Server är för närvarande en för hands version.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och välj **RETUR** för att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/reference-index#az-login) . Observera egenskapen **ID** som refererar till **prenumerations-ID** för ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account#az-account-set) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Skapa en flexibel server

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md) med `az group create` kommandot och skapa sedan din postgresql-flexibla server i den här resurs gruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Skapa en flexibel server med `az postgres flexible-server create` kommandot. En server kan innehålla flera databaser. Följande kommando skapar en server som använder tjänstens standardinställningar och värden från Azure CLI: s [lokala kontext](/cli/azure/local-context): 

```azurecli
az postgres flexible-server create
```

Servern som har skapats har följande attribut: 
- Automatiskt genererat Server namn, administratörs användar namn, administratörs lösen ord, resurs grupp namn (om det inte redan har angetts i den lokala kontexten) och på samma plats som resurs gruppen 
- Standardinställningar för tjänsten för återstående serverkonfigurationer: beräknings nivå (Generell användning), beräknings storlek/SKU (D2s_v3-2 vCore, 8 GB RAM), kvarhållning av säkerhets kopior (7 dagar) och PostgreSQL-version (12)
- Standard anslutnings metoden är privat åtkomst (VNet-integrering) med ett automatiskt genererat virtuellt nätverk och undernät

> [!NOTE] 
> Anslutnings metoden kan inte ändras när servern har skapats. Om du till exempel har valt *privat åtkomst (VNet-integrering)* under Create kan du inte ändra till *offentlig åtkomst (tillåtna IP-adresser)* när du har skapat. Vi rekommenderar starkt att du skapar en server med privat åtkomst för säker åtkomst till servern med VNet-integrering. Läs mer om privat åtkomst i [artikeln begrepp](./concepts-networking.md).

Om du vill ändra några standardvärden, se referens dokumentationen för Azure CLI <!--FIXME --> en fullständig lista över konfigurerbara CLI-parametrar. 

> [!NOTE]
> Anslutningar till Azure Database for PostgreSQL kommunicera via port 5432. Om du försöker ansluta inifrån ett företags nätverk kanske utgående trafik via port 5432 inte tillåts. I så fall kan du inte ansluta till servern om inte din IT-avdelning öppnar port 5432.

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

## <a name="connect-using-postgresql-command-line-client"></a>Anslut med PostgreSQL kommando rads klient

Eftersom den flexibla servern skapades med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i det virtuella nätverk som skapats. 

När den virtuella datorn har skapats kan du använda SSH i datorn och installera kommando rads verktyget **[psql](https://www.postgresql.org/download/)** .

Med psql ansluter du med kommandot nedan. Ersätt värden med det faktiska Server namnet och lösen ordet. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du bara vill ta bort den nyligen skapade servern kan du köra `az postgres flexible-server delete` kommandot.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Distribuera en django-app med App Service och PostgreSQL](tutorial-django-app-service-postgres.md)
