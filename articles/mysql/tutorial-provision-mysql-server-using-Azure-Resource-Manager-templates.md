---
title: 'Självstudie: Skapa Azure Database for MySQL-Azure Resource Manager mall'
description: Den här självstudien beskriver hur du etablerar och automatiserar Azure Database for MySQL-serverdistributioner med Azure Resource Manager-mallar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705480"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Självstudie: etablera en Azure Database for MySQL server med Azure Resource Manager-mall

[Azure Database for MySQL REST API](/rest/api/mysql/) låter DevOps-tekniker automatisera och integrera etablering, konfiguration och drift för hanterade MySQL-servrar och databaser i Azure.  API:et låter dig skapa, uppräkna, hantera och ta bort MySQL-servrar och databaser på Azure Database for MySQL-tjänsten.

Azure Resource Manager använder den underliggande REST API för att deklarera och program de Azure-resurser som krävs för distributioner i skala, anpassas med infrastruktur som ett kod begrepp. Mallen parameteriserar Azure-resursens namn, SKU, nätverk, brandväggskonfiguration och inställningar, vilket låter den skapas en gång och användas flera gånger.  Azure Resource Manager-mallar kan enkelt skapas med [Azure-portalen](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) eller [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI). De möjliggör programpaketering, standardisering och distributionsautomatisering som kan integreras i DevOps CI/CD-pipelinen.  Om du till exempel snabbt vill distribuera en Webbapp med en Azure Database for MySQL-serverdel så kan du utföra distributionen från början till slut med den här [Snabbstartsmallen](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) från GitHub-galleriet.

I den här självstudien ska du använda Azure Resource Manager-mallar och andra verktyg för att lära dig:

> [!div class="checklist"]
> * Skapa en Azure Database for MySQL-server med VNet-tjänstslutpunkt med en Azure Resource Manager-mall
> * Använd [kommando rads verktyget MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Skapa en Azure Database for MySQL-server med VNet-tjänstslutpunkt med en Azure Resource Manager-mall

JSON-mallreferensen för en Azure Database for MySQL-server finns under mallreferensen för [Microsoft.DBforMySQL-servrar](/azure/templates/microsoft.dbformysql/servers). Nedan visas JSON-exempelmallen som kan användas för att skapa en ny server som kör Azure Database for MySQL med VNet-tjänstslutpunkt.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
I den här begäran är de värden som måste anpassas:
+   `name` – Ange namnet på din MySQL-server (utan domännamn).
+   `location` – Ange en giltig Azure-datacenterregion för din MySQL-server. Till exempel westus2.
+   `properties/version` – Ange den MySQL-serverversion som ska distribueras. Till exempel 5.6 eller 5.7.
+   `properties/administratorLogin` – Ange MySQL administratörsinloggningen till servern. Inloggningsnamnet för administratören får inte vara azure_superuser, admin, administrator, root, guest eller public.
+   `properties/administratorLoginPassword` – Ange lösenordet för den MySQL-administratörsanvändare som anges ovan.
+   `properties/sslEnforcement` – Ange aktiverat/inaktiverat för att aktivera/inaktivera sslEnforcement.
+   `storageProfile/storageMB` – Ange den högsta allokerade lagringsstorleken som krävs för servern i megabyte. Till exempel 5120.
+   `storageProfile/backupRetentionDays` – Ange den önskade kvarhållningsperioden för säkerhetskopior i dagar. Till exempel 7. 
+   `storageProfile/geoRedundantBackup` – Ange aktiverat/inaktiverat beroende på kraven för geohaveriberedskap.
+   `sku/tier` – Ange nivån Basic, GeneralPurpose eller MemoryOptimized för distributionen.
+   `sku/capacity` – Ange vCore-kapacitet. Möjliga värden är 2, 4, 8, 16, 32 eller 64.
+   `sku/family` -Ange Gen5 för att välja maskin varu generering för Server distribution.
+   `sku/name` – Ange TierPrefix_family_capacity. Till exempel B_Gen5_1 GP_Gen5_16 MO_Gen5_32. Se dokumentationen [prissättningsnivåer](./concepts-pricing-tiers.md) för att se giltiga värden per region och nivå.
+   `resources/properties/virtualNetworkSubnetId` – Ange Azure-identifieraren för undernätet i det virtuella nätverket där Azure MySQL-servern ska placeras. 
+   `tags(optional)` – Ange valfria taggar är nyckelvärdepar som du använder för att kategorisera resurser för fakturering osv.

Om du vill skapa en Azure Resource Manager-mall för att automatisera Azure Database for MySQL-distributioner för din organisation så är rekommendationen att börja från exemplet [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) i Azure-snabbstartsgalleriet i GitHub först och bygga på den. 

Om du är nybörjare på Azure Resource Manager-mallar och vill prova så kan du starta genom att följa dessa steg:
+   Klona eller hämta exemplet [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) från Azure-snabbstartsgalleriet.  
+   Ändra azuredeploy.parameters.json om du vill uppdatera parametervärdena baserat på dina inställningar och spara filen. 
+   Använd Azure CLI för att skapa Azure MySQL-servern med hjälp av följande kommandon

Du kan använda Azure Cloud Shell i webbläsaren eller installera Azure CLI på din egen dator för att köra kodblocken i den här självstudien.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen
För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Anslut till servern med mysql
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att upprätta en anslutning till din Azure Database for MySQL-server. I det här exemplet är kommandot:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern ska du skapa en tom databas.
```sql
mysql> CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for MySQL-databasen kan du utföra några grundläggande uppgifter.

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läs in data i tabellerna
Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande frågor för att hämta information från databastabellen.
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **resurs grupper**.

2. I listan resurs grupp väljer du namnet på din resurs grupp.

3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp**.

4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Nästa steg
I de här självstudierna lärde du dig att:
> [!div class="checklist"]
> * Skapa en Azure Database for MySQL-server med VNet-tjänstslutpunkt med en Azure Resource Manager-mall
> * Använda kommandoradsverktyget mysql till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data

> [!div class="nextstepaction"]
> [Så ansluter du program till Azure Database for MySQL](./howto-connection-string.md)