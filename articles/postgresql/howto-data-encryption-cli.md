---
title: Data kryptering – Azure CLI – för Azure Database for PostgreSQL-enskild server
description: Lär dig hur du konfigurerar och hanterar data kryptering för Azure Database for PostgreSQL enskild server med hjälp av Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240621"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Data kryptering för Azure Database for PostgreSQL enskild server med hjälp av Azure CLI

Lär dig hur du använder Azure CLI för att konfigurera och hantera data kryptering för din Azure Database for PostgreSQL enskild server.

## <a name="prerequisites-for-azure-cli"></a>Krav för Azure CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* Skapa ett nyckel valv och en nyckel som ska användas för en kundhanterad nyckel. Aktivera även rensnings skydd och mjuk borttagning i nyckel valvet.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* I den skapade Azure Key Vault skapar du den nyckel som ska användas för data kryptering av Azure Database for PostgreSQL enskild server.

   ```azurecli-interactive
   az keyvault key create --name <key_name> -p software --vault-name <vault_name>
   ```

* För att kunna använda ett befintligt nyckel valv måste följande egenskaper användas som en kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/general/soft-delete-overview.md)

      ```azurecli-interactive
      az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
      ```

  * [Rensa skyddad](../key-vault/general/soft-delete-overview.md#purge-protection)

      ```azurecli-interactive
      az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
      ```

* Nyckeln måste ha följande attribut för att användas som en kundhanterad nyckel:
  * Inget förfallodatum
  * Inte aktiverat
  * Utföra **Get**-, **wrap** -och **unwrap** -åtgärder

## <a name="set-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för viktiga åtgärder

1. Det finns två sätt att hämta den hanterade identiteten för Azure Database for PostgreSQL enskild server.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Skapa en ny Azure Database for PostgreSQL-server med en hanterad identitet.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Uppdatera en befintlig Azure Database for PostgreSQL-Server för att få en hanterad identitet.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Ange **nyckel behörigheter** (**Get** **, wrapping**, **unwrap**) för **huvud kontot**, som är namnet på postgresql-servern.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ange data kryptering för Azure Database for PostgreSQL enskild server

1. Aktivera data kryptering för Azure Database for PostgreSQL enskild server med den nyckel som skapats i Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Nyckel-URL:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Använda data kryptering för återställning eller replik servrar

När Azure Database for PostgreSQL enskild server har krypterats med en kunds hanterade nyckel som lagras i Key Vault krypteras även alla nyligen skapade kopior av servern. Du kan göra den här nya kopian antingen via en lokal åtgärd eller geo-återställning eller via en replikering (lokal/över region) åtgärd. För en krypterad PostgreSQL-Server kan du använda följande steg för att skapa en krypterad återställd Server.

### <a name="creating-a-restoredreplica-server"></a>Skapa en återställd/replik Server

* [Skapa en återställnings Server](howto-restore-server-cli.md)
* [Skapa en Läs replik Server](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>När servern har återställts verifierar du om data krypteringen på den återställda servern

*   Tilldela identitet för replik servern
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Hämta den befintliga nyckeln som ska användas för den återställda/replik servern

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Ange principen för den nya identiteten för den återställda/replik servern

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Verifiera om den återställda/replik servern med krypterings nyckeln

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Ytterligare funktion för den nyckel som används för Azure Database for PostgreSQL enskild server

### <a name="get-the-key-used"></a>Hämta nyckeln som används

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Nyckel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Visa en lista över den nyckel som används

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Ta bort den nyckel som används

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Använda en Azure Resource Manager-mall för att aktivera data kryptering

Förutom Azure Portal kan du också aktivera data kryptering på din Azure Database for PostgreSQL enskild server med Azure Resource Manager mallar för nya och befintliga servrar.

### <a name="for-a-new-server"></a>För en ny server

Använd en av de redan skapade Azure Resource Manager-mallarna för att etablera servern med data kryptering aktiverat: [exempel med data kryptering](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Den här Azure Resource Manager-mallen skapar en Azure Database for PostgreSQL enskild server och använder nyckel **valvet** och **nyckeln** som skickas som parametrar för att aktivera data kryptering på servern.

### <a name="for-an-existing-server"></a>För en befintlig server
Du kan också använda Azure Resource Manager mallar för att aktivera data kryptering på dina befintliga Azure Database for PostgreSQL-servrar.

* Skicka resurs-ID: t för den Azure Key Vault nyckel som du kopierade tidigare under `Uri` egenskapen i objektet egenskaper.

* Använd *2020-01-01 – för hands* version som API-version.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

 Läs mer om data kryptering i [Azure Database for PostgreSQL data kryptering med en server med kundhanterad nyckel](concepts-data-encryption-postgresql.md).
