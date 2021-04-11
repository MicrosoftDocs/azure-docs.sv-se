---
title: Så här aktiverar du Azure Key Vault loggning
description: Så här aktiverar du loggning för Azure Key Vault, som sparar information i ett Azure Storage-konto som du anger.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 62035b2fe6c3db71e392a05946ea3f230dfa030e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604666"
---
# <a name="how-to-enable-key-vault-logging"></a>Så här aktiverar du Key Vault loggning

När du har skapat ett eller flera nyckel valv vill du förmodligen övervaka hur och när nyckel valven nås, och av vem. Fullständig information om funktionen finns i [Key Vault loggning](logging.md).

Vad loggas:

* Alla autentiserade REST API begär Anden, inklusive misslyckade förfrågningar till följd av åtkomst behörigheter, systemfel eller felaktiga begär Anden.
* Åtgärder i själva nyckel valvet, inklusive att skapa, ta bort, ställa in åtkomst principer för nyckel valv och uppdatera Key Vault-attribut som taggar.
* Åtgärder för nycklar och hemligheter i nyckel valvet, inklusive:
  * Skapa, ändra eller ta bort nycklar eller hemligheter.
  * Signering, verifiering, kryptering, dekryptering, wrapping och unwrap-nycklar, Hämta hemligheter och Visa nycklar och hemligheter (och deras versioner).
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begär Anden som inte har en Bearer-token, som har fel format eller som har upphört att gälla eller som har en ogiltig token.  
* Event Grid meddelande händelser för snart utgångs datum, upphör ande och åtkomst principen för valv har ändrats (ny versions händelse loggas inte). Händelser loggas oavsett om det finns en händelse prenumeration som skapats i Key Vault. Mer information finns i [Event Grid händelse schema för Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett befintligt nyckelvalv som du har använt.  
* [Azure Cloud Shell](https://shell.azure.com) -bash-miljö
* Tillräckligt med utrymme i Azure för Key Vault-loggarna.

De här guide kommandona är formaterade för [Cloud Shell](https://shell.azure.com) med bash som en miljö.

## <a name="connect-to-your-key-vault-subscription"></a>Ansluta till din Key Vault-prenumeration

Det första steget när du ställer in nyckel loggningen är att ansluta till prenumerationen som innehåller ditt nyckel valv. Detta är särskilt viktigt om du har flera prenumerationer som är kopplade till ditt konto.

Med Azure CLI kan du Visa alla dina prenumerationer med kommandot [AZ Account List](/cli/azure/account#az_account_list) och sedan ansluta till en med [AZ-konto uppsättning](/cli/azure/account#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Med Azure PowerShell kan du först lista dina prenumerationer med hjälp av cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) och sedan ansluta till en med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Skapa ett lagrings konto för dina loggar

Även om du kan använda ett befintligt lagrings konto för loggarna skapar vi ett nytt lagrings konto som är dedikerat för Key Vault loggar. 

För ytterligare enkel hantering kommer vi också att använda samma resurs grupp som den som innehåller nyckel valvet. I snabb starten av [Azure CLI](quick-create-cli.md) och [Azure PowerShell snabb](quick-create-powershell.md)starten heter den här resurs gruppen **myResourceGroup** och platsen är *öster*. Ersätt värdena med dina egna, efter vad som är tillämpligt. 

Vi kommer också att behöva ange ett lagrings konto namn. Lagrings konto namn måste vara unika, vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.  Slutligen kommer vi att skapa ett lagrings konto för SKU: n "Standard_LRS".

Med Azure CLI använder du kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Med Azure PowerShell använder du cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Du måste ange den plats som motsvarar resurs gruppen.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

I båda fallen noterar du "ID" för lagrings kontot. Azure CLI-åtgärden returnerar "ID" i utdata. Om du vill hämta "ID" med Azure PowerShell använder du [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) och tilldelade utdata till en variabel $sa. Du kan sedan se lagrings kontot med $sa. ID. ($Sa. Context "-egenskapen kommer också att användas, längre fram i den här artikeln.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

"ID" för lagrings kontot kommer att ha formatet "/Subscriptions/<Your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<ditt unika lagrings konto namn>".

> [!NOTE]
> Om du väljer att använda ett befintligt lagrings konto måste det använda samma prenumeration som nyckel valvet, och det måste använda Azure Resource Manager distributions modell i stället för den klassiska distributions modellen.

## <a name="obtain-your-key-vault-resource-id"></a>Hämta ditt Key Vault-resurs-ID

I snabb starten för [CLI](quick-create-cli.md) och [PowerShell](quick-create-powershell.md)skapade du en nyckel med ett unikt namn.  Använd det namnet igen i stegen nedan.  Om du inte kommer ihåg namnet på ditt nyckel valv kan du använda kommandot Azure CLI [AZ Key Vault List](/cli/azure/keyvault#az_keyvault_list) eller cmdleten Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) för att lista dem.

Använd namnet på ditt nyckel valv för att hitta dess resurs-ID.  Med Azure CLI använder du kommandot [AZ Command Vault show](/cli/azure/keyvault#az_keyvault_show) .

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Med Azure PowerShell använder du cmdleten [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

Resurs-ID för nyckel valvet kommer att vara i formatet "/Subscriptions/<Your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<ditt-Unique-Key-namn>". Lägg märke till nästa steg.

## <a name="enable-logging"></a>Aktivera loggning

Du kan aktivera loggning för Key Vault med hjälp av Azure CLI, Azure PowerShell eller Azure Portal.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="azure-cli"></a>Azure CLI

Använd Azure CLI [-AZ övervaka diagnostiskt-Settings Create](/cli/azure/monitor/diagnostic-settings) tillsammans med lagrings konto-ID: t och Key Vault-resurs-ID: t.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Du kan också ange en bevarande princip för loggarna så att äldre loggar tas bort automatiskt efter en viss tid. Du kan till exempel ange en bevarande princip som automatiskt tar bort loggar som är äldre än 90 dagar.

Med Azure CLI använder du kommandot [AZ Monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) . 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Använd cmdleten [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) med flaggan **-Enabled** inställd på **$True** och kategorin inställd på `AuditEvent` (den enda kategorin för Key Vault loggning):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Du kan också ange en bevarande princip för loggarna så att äldre loggar tas bort automatiskt efter en viss tid. Du kan till exempel ange en bevarande princip som automatiskt tar bort loggar som är äldre än 90 dagar.

Med Azure PowerShell använder du cmdleten [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) .

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

Följ dessa steg om du vill konfigurera diagnostikinställningar i portalen.

1. Välj diagnostikinställningar på menyn resurs blad.

    :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="Diagnostisk Portal 1":::

1. Klicka på inställningen "+ Lägg till diagnostik"

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="Diagnostisk Portal 2":::
 
1. Välj ett namn för att anropa den diagnostiska inställningen. Om du vill konfigurera loggning för Azure Monitor för Key Vault väljer du alternativet "AuditEvent" och "Skicka till Log Analytics arbets yta". Välj sedan den prenumeration och Log Analytics arbets yta som du vill skicka loggarna.

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="Diagnostisk Portal 3":::

    Annars väljer du de alternativ som är kopplade till de loggar som du vill välja

1. När du har valt önskade alternativ väljer du spara.

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="Diagnostisk Portal 4":::

---

## <a name="access-your-logs"></a>Komma åt loggarna

Key Vault loggar lagras i behållaren "Insights-logs-auditevent" i det lagrings konto som du har angett. Om du vill visa loggarna måste du ladda ned blobbar.

Först visar lista alla blobar i behållaren.  Med Azure CLI använder du kommandot [AZ Storage BLOB List](/cli/azure/storage/blob#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Med Azure PowerShell använder du listan [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) alla blobar i den här behållaren, ange:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Som du ser från utdata från ett Azure CLI-kommando eller en Azure PowerShell-cmdlet, är namnet på blobarna i formatet `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . Datum- och tidsvärdena använder UTC.

Eftersom du kan använda samma lagrings konto för att samla in loggar för flera resurser, är det fullständiga resurs-ID: t i BLOB-namnet användbart för att få åtkomst till eller hämta bara de blobbar som du behöver. Men innan vi gör det ska vi titta på hur du hämtar alla blobbar.

Med Azure CLI använder du kommandot [AZ Storage BLOB Download](/cli/azure/storage/blob#az_storage_blob_download) , skickar det till namnet på Blobbarna och sökvägen till filen där du vill spara resultatet.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Med Azure PowerShell använder du [AzStorageBlobs-cmdleten gt-](/powershell/module/az.storage/get-azstorageblob) för att hämta en lista över blobarna. därefter når du cmdleten [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) för att hämta loggarna till den valda sökvägen.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

När du kör den här andra cmdleten i PowerShell **/** skapar avgränsaren i BLOB-namnen en fullständig mappstruktur under målmappen. Du använder den här strukturen för att ladda ned och lagra Blobbarna som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera nyckelvalv och bara vill hämta loggar för ett av dem, mer specifikt nyckelvalvet CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Om du har flera resursgrupper och bara vill hämta loggar för en av dem använder du `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Om du vill hämta alla loggar i januari 2019 använder du `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nu är det dags att börja titta på vad som finns i loggarna. Men innan vi går vidare till så bör du känna till två kommandon:

Mer information om hur du läser loggarna finns i [Key Vault Logging: tolka dina Key Vaults loggar](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor loggar för att granska Key Vault `AuditEvent` loggar. I Azure Monitor loggar använder du logg frågor för att analysera data och få den information du behöver.

Mer information, inklusive hur du konfigurerar detta finns i [Azure Key Vault i Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

- För konceptuell information, inklusive hur du tolkar Key Vault loggar, se [Key Vault loggning](logging.md)
- En själv studie kurs som använder Azure Key Vault i ett .NET-webb program finns i [använda Azure Key Vault från ett webb program](tutorial-net-create-vault-azure-web-app.md).
- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](developers-guide.md).
