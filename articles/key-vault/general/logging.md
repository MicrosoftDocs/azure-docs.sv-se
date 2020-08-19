---
title: Azure Key Vault loggning | Microsoft Docs
description: Lär dig hur du övervakar åtkomst till dina nyckel valv genom att aktivera loggning för Azure Key Vault, som sparar information i ett Azure Storage-konto som du anger.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: e9507525dc2c52f584bd7883a12da401b5999f50
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585924"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

När du har skapat ett eller flera nyckel valv vill du förmodligen övervaka hur och när nyckel valven nås, och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, som sparar information i ett Azure Storage-konto som du anger. En ny behållare med namnet **Insights-logs-auditevent** skapas automatiskt för det angivna lagrings kontot. Du kan använda samma lagrings konto för att samla in loggar för flera nyckel valv.

Du kan komma åt loggnings informationen 10 minuter (högst) efter nyckel valvs åtgärden. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Den här kursen hjälper dig att komma igång med Azure Key Vault-loggning. Du skapar ett lagrings konto, aktiverar loggning och tolkar insamlad logg information.  

> [!NOTE]
> Självstudiekursen innehåller inte instruktioner för hur du skapar nyckelvalv, nycklar eller hemligheter. Mer information finns i [Vad är Azure Key Vault?](overview.md)). Eller, för plattforms oberoende Azure CLI-instruktioner, se [motsvarande självstudie](manage-with-cli2.md).
>
> Den här artikeln innehåller Azure PowerShell instruktioner för uppdatering av diagnostisk loggning. Du kan också uppdatera diagnostikloggning genom att använda Azure Monitor i avsnittet **diagnostiska loggar** i Azure Portal. 
>

Översiktlig information om Key Vault finns i [Vad är Azure Key Vault?](overview.md)). Information om var Key Vault finns på [sidan med priser](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett befintligt nyckelvalv som du har använt.  
* Azure PowerShell, lägsta versionen av 1.0.0. Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/). Om du redan har installerat Azure PowerShell och inte känner till versionen från Azure PowerShell-konsolen anger du `$PSVersionTable.PSVersion` .  
* Tillräckligt med utrymme i Azure för Key Vault-loggarna.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Anslut till din Key Vault-prenumeration

Det första steget när du ställer in nyckel loggning är att peka Azure PowerShell till det nyckel valv som du vill logga.

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med hjälp av följande kommando:  

```powershell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell hämtar alla prenumerationer som är associerade med det här kontot. Som standard använder PowerShell den första.

Du kanske måste ange den prenumeration som du använde för att skapa nyckel valvet. Ange följande kommando för att se prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Om du vill ange den prenumeration som är associerad med nyckel valvet som ska loggas, anger du:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Att peka PowerShell till rätt prenumeration är ett viktigt steg, särskilt om du har flera prenumerationer som är kopplade till ditt konto. Mer information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Skapa ett lagrings konto för dina loggar

Även om du kan använda ett befintligt lagrings konto för dina loggar, kommer vi att skapa ett lagrings konto som ska användas för Key Vault loggar. För att du ska kunna ange detta senare kommer vi att lagra informationen i en variabel med namnet **sa**.

För ytterligare enkel hantering kommer vi också att använda samma resurs grupp som den som innehåller nyckel valvet. I [självstudien komma igång](../secrets/quick-create-cli.md)heter den här resurs gruppen **ContosoResourceGroup**och vi fortsätter att använda den Asien, östra platsen. Ersätt värdena med dina egna, i tillämpliga fall:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Om du väljer att använda ett befintligt lagrings konto måste det använda samma prenumeration som nyckel valvet. Och den måste använda Azure Resource Manager distributions modell i stället för den klassiska distributions modellen.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identifiera nyckelvalvet för dina loggar

I [självstudien komma igång](../secrets/quick-create-cli.md)var nyckel valv namnet **ContosoKeyVault**. Vi kommer att fortsätta använda det namnet och lagra informationen i en variabel med namnet **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>Aktivera loggning med Azure PowerShell

Om du vill aktivera loggning för Key Vault använder vi cmdleten **set-AzDiagnosticSetting** tillsammans med de variabler som vi skapade för det nya lagrings kontot och nyckel valvet. Vi ställer också in flaggan **-Enabled** till **$True** och anger kategorin till `AuditEvent` (den enda kategorin för Key Vault loggning):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Utdata ser ut så här:

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

Det här resultatet bekräftar att loggning har Aktiver ATS för nyckel valvet och sparar information till ditt lagrings konto.

Du kan också ange en bevarande princip för dina loggar, så att äldre loggar tas bort automatiskt. Ange till exempel princip för bevarande genom att ange flaggan **-RetentionEnabled** till **$True**och ange parametern **-RetentionInDays** till **90** så att loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Vad loggas:

* Alla autentiserade REST API begär Anden, inklusive misslyckade förfrågningar till följd av åtkomst behörigheter, systemfel eller felaktiga begär Anden.
* Åtgärder i själva nyckel valvet, inklusive att skapa, ta bort, ställa in åtkomst principer för nyckel valv och uppdatera Key Vault-attribut som taggar.
* Åtgärder för nycklar och hemligheter i nyckel valvet, inklusive:
  * Skapa, ändra eller ta bort nycklar eller hemligheter.
  * Signering, verifiering, kryptering, dekryptering, wrapping och unwrap-nycklar, Hämta hemligheter och Visa nycklar och hemligheter (och deras versioner).
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begär Anden som inte har en Bearer-token, som har fel format eller som har upphört att gälla eller som har en ogiltig token.  

## <a name="enable-logging-using-azure-cli"></a>Aktivera loggning med Azure CLI

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>Komma åt loggarna

Key Vault loggar lagras i behållaren **Insights-logs-auditevent** i det lagrings konto som du har angett. Om du vill visa loggarna måste du ladda ned blobbar.

Börja med att skapa en variabel för containerns namn. Du använder den här variabeln i resten av genom gången.

```powershell
$container = 'insights-logs-auditevent'
```

Om du vill visa en lista över alla blobar i den här behållaren anger du:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Utdata ser ut ungefär så här:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Som du kan se från dessa utdata följer Blobbarna en namngivnings konvention: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom du kan använda samma lagrings konto för att samla in loggar för flera resurser, är det fullständiga resurs-ID: t i BLOB-namnet användbart för att få åtkomst till eller hämta bara de blobbar som du behöver. Men innan vi gör det ska vi titta på hur du hämtar alla blobbar.

Skapa en mapp för att ladda ned Blobbarna. Exempel:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Hämta sedan en lista över alla blobbar:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Använd den här listan via **Get-AzStorageBlobContent** för att ladda ned blobbar till målmappen:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

När du kör det här andra kommandot **/** skapar avgränsaren i BLOB-namnen en fullständig mappstruktur under målmappen. Du använder den här strukturen för att ladda ned och lagra Blobbarna som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera nyckelvalv och bara vill hämta loggar för ett av dem, mer specifikt nyckelvalvet CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Om du har flera resursgrupper och bara vill hämta loggar för en av dem använder du `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Om du vill hämta alla loggar i januari 2019 använder du `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nu är det dags att börja titta på vad som finns i loggarna. Men innan vi går vidare till så bör du känna till två kommandon:

* Om du vill fråga efter statusen för nyckelvalvsresursens diagnostikinställningar: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Om du vill inaktivera loggning för nyckelvalvsresursen: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Tolka Key Vault-loggarna

Enskilda blobbar lagras som text, formaterad som en JSON-blobb. Nu ska vi titta på en exempel logg post. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

I följande tabell visas fält namn och beskrivningar:

| Fältnamn | Description |
| --- | --- |
| **tid** |Datum och tid i UTC. |
| **resourceId** |Azure Resource Manager resurs-ID. För Key Vault loggar är detta alltid Key Vault resurs-ID. |
| **operationName** |Namnet på åtgärden, som beskrivs i nästa tabell. |
| **operationVersion** |REST API version som begärs av klienten. |
| **kategori** |Typ av resultat. För Key Vault loggar `AuditEvent` är det enda, tillgängliga värdet. |
| **resultType** |Resultat av den REST API begäran. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| **. Durationms** |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| **callerIpAddress** |IP-adressen för den klient som gjorde begäran. |
| **correlationId** |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| **Autentiseringsidentitet** |Identitet från den token som angavs i REST API begäran. Detta är vanligt vis en "användare", "tjänstens huvud namn" eller kombinationen "användare + appId", som i fallet med en begäran som resulterar från en Azure PowerShell-cmdlet. |
| **egenskaperna** |Information som varierar beroende på åtgärd (**operationName**). I de flesta fall innehåller det här fältet klient information (den användar agent sträng som skickas av klienten), exakt REST API begär ande-URI och HTTP-statuskod. När ett objekt returneras som ett resultat av en begäran (till exempel nyckel **skapa** eller **VaultGet**), innehåller det även nyckel-URI (as `id` ), valv-URI eller hemlig URI. |

Värdena för **operationName** -fältet är i *ObjectVerb* -format. Exempel:

* Alla Key Vault-åtgärder har `Vault<action>` formatet, till exempel `VaultGet` och `VaultCreate` .
* Alla viktiga åtgärder har `Key<action>` formatet, till exempel `KeySign` och `KeyList` .
* Alla hemliga åtgärder har `Secret<action>` formatet, till exempel `SecretGet` och `SecretListVersions` .

I följande tabell visas **operationName** -värdena och motsvarande REST API-kommandon:

| operationName | REST API kommando |
| --- | --- |
| **Autentisering** |Autentisera via Azure Active Directory slut punkt |
| **VaultGet** |[Hämta information om ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Skapa eller uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Ta bort ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Visa en lista med alla nyckelvalv i en resursgrupp](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Skapa en nyckel](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Hämta information om en nyckel](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importera en nyckel till ett valv](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Säkerhetskopiera en nyckel](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Ta bort en nyckel](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Återställa en nyckel](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Signera med en nyckel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verifiera med en nyckel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Omsluta en nyckel](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Ta bort en nyckelomslutning](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Kryptera med en nyckel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Dekryptera med en nyckel](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Uppdatera en nyckel](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Visa en lista med nycklarna i ett valv](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Visa en lista över versionerna av en nyckel](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Skapa en hemlighet](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Få en hemlighet](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Uppdatera en hemlighet](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Ta bort en hemlighet](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Visa en lista över hemligheterna i ett valv](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Visa en lista över versionerna av en hemlighet](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor loggar för att granska Key Vault `AuditEvent` loggar. I Azure Monitor loggar använder du logg frågor för att analysera data och få den information du behöver. 

Mer information, inklusive hur du konfigurerar detta finns i [Azure Key Vault i Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>Nästa steg

En själv studie kurs som använder Azure Key Vault i ett .NET-webb program finns i [använda Azure Key Vault från ett webb program](tutorial-net-create-vault-azure-web-app.md).

Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](developers-guide.md).

En lista över Azure PowerShell 1,0-cmdletar för Azure Key Vault finns i [Azure Key Vault-cmdletar](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
