---
title: Rotera TDE-skydd (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Lär dig hur du roterar transparent datakryptering skydd (TDE) för en server i Azure som används av Azure SQL Database och Azure Synapse Analytics med hjälp av PowerShell och Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 67bcd8597314530f26481ef840644ffbc056b033
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777571"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Rotera transparent datakryptering (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


I den här artikeln beskrivs nyckelrotation för en [server som](logical-servers.md) använder ett TDE-skydd från Azure Key Vault. Att rotera det logiska TDE-skyddet för en server innebär att växla till en ny asymmetrisk nyckel som skyddar databaserna på servern. Nyckelrotation är en onlineåtgärd som bara bör ta några sekunder att slutföra, eftersom detta endast dekrypterar och omkrypterar databasens datakrypteringsnyckel, inte hela databasen.

I den här guiden beskrivs två alternativ för att rotera TDE-skyddet på servern.

> [!NOTE]
> En pausad dedikerad SQL-pool Azure Synapse Analytics måste återupptas innan nyckelrotationerna.

> [!IMPORTANT]
> Ta inte bort tidigare versioner av nyckeln efter en återställning. När nycklar återställs krypteras fortfarande vissa data med de tidigare nycklarna, till exempel äldre databassäkerhetskopior.

## <a name="prerequisites"></a>Förutsättningar

- Den här guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för Azure SQL Database eller Azure Synapse Analytics. Se [transparent datakryptering byok-stöd](transparent-data-encryption-byok-overview.md).
- Du måste ha Azure PowerShell installerat och igång.
- [Rekommenderas men är valfritt] Skapa nyckelmaterialet för TDE-skyddet i en maskinvarusäkerhetsmodul (HSM) eller lokalt nyckelarkiv först och importera nyckelmaterialet till Azure Key Vault. Följ [anvisningarna för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](../../key-vault/general/overview.md) mer information.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). Specifika cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager (RM) stöds fortfarande, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot felkorrigeringar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Information om installation finns [i Installera Azure CLI.](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>Manuell nyckelrotation

Manuell nyckelrotation använder följande kommandon för att lägga till en helt ny nyckel, som kan finnas under ett nytt nyckelnamn eller till och med ett annat nyckelvalv. Med den här metoden kan du lägga till samma nyckel i olika nyckelvalv för att stödja scenarier med hög tillgänglighet och geo-dr.

> [!NOTE]
> Den kombinerade längden för nyckelvalvets namn och nyckelnamn får inte överskrida 94 tecken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd cmdletarna [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)och [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [kommandona az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create), [az sql server key create](/cli/azure/sql/server/key#az_sql_server_key_create)och az sql server [tde-key set.](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Växla TDE-skyddsläge

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Om du vill växla TDE-skydd från Microsoft-hanterat till BYOK-läge använder du cmdleten [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Om du vill växla TDE-skydd från BYOK-läge till Microsoft-hanterat använder du cmdleten [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

I följande exempel används [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Om du vill växla TDE-skydd från Microsoft-hanterat till BYOK-läge,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Om du vill växla TDE-skydd från BYOK-läge till Microsoft-hanterat,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Nästa steg

- Om det finns en säkerhetsrisk kan du lära dig hur du tar bort ett potentiellt komprometterat TDE-skydd: [Ta bort en potentiellt komprometterad nyckel.](transparent-data-encryption-byok-remove-tde-protector.md)

- Kom igång med Azure Key Vault integrering och Bring Your Own Key för TDE: Aktivera TDE med din egen nyckel från [Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-configure.md).