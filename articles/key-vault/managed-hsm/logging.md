---
title: Azure-hanterad HSM-loggning
description: Använd den här självstudien för att komma igång med hanterad HSM-loggning.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7420ffbe5b365c635c1eac2620cfd54ceb649ebf
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211812"
---
# <a name="managed-hsm-logging"></a>Hanterad HSM-loggning 

När du har skapat en eller flera hanterade HSM: er vill du förmodligen övervaka hur och när dina HSMss används, och av vem. Du kan göra detta genom att aktivera loggning, som sparar information i ett Azure Storage-konto som du anger. En ny behållare med namnet **Insights-logs-auditevent** skapas automatiskt för det angivna lagrings kontot. Du kan använda samma lagrings konto för att samla in loggar för flera hanterade HSM: er.

Du kan komma åt loggnings informationen 10 minuter (högst) efter den hanterade HSM-åtgärden. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna på ditt lagringskonto:

* Använd åtkomstkontrollsmetoder för Azure av standardtyp för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte längre vill behålla på ditt lagringskonto.

Använd den här självstudien för att komma igång med hanterad HSM-loggning. Du skapar ett lagrings konto, aktiverar loggning och tolkar insamlad logg information.  

> [!NOTE]
> Den här själv studie kursen innehåller inte instruktioner för hur du skapar hanterade HSM: er eller nycklar. Den här artikeln innehåller Azure CLI-instruktioner för uppdatering av diagnostisk loggning.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln, måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI-versionen 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).
* En hanterad HSM i din prenumeration. Se [snabb start: etablera och aktivera en hanterad HSM med Azure CLI](quick-create-cli.md) för att etablera och aktivera en hanterad HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Det första steget när du ställer in nyckel loggning är att peka Azure CLI till den hanterade HSM som du vill logga.

```azurecli-interactive
az login
```

Mer information om inloggningsalternativen via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)

Du kanske måste ange den prenumeration som du använde för att skapa en hanterad HSM. Ange följande kommando för att se prenumerationerna för ditt konto:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identifiera hanterad HSM och lagrings konto

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Aktivera loggning

Om du vill aktivera loggning för hanterad HSM använder du kommandot **AZ Monitor Diagnostic-Settings Create** tillsammans med de variabler som vi skapade för det nya lagrings kontot och den hanterade HSM: en. Vi ställer också in flaggan **-Enabled** till **$True** och anger kategorin till **AuditEvent** (den enda kategorin för hanterad HSM-loggning):

Det här resultatet bekräftar att loggning nu har Aktiver ATS för din hanterade HSM och sparar information till ditt lagrings konto.

Du kan också ange en bevarande princip för dina loggar, så att äldre loggar tas bort automatiskt. Ange till exempel princip för bevarande genom att ange flaggan **-RetentionEnabled** till **$True** och ange parametern **-RetentionInDays** till **90** så att loggar som är äldre än 90 dagar tas bort automatiskt.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Vad loggas:

* Alla autentiserade REST API begär Anden, inklusive misslyckade förfrågningar till följd av åtkomst behörigheter, systemfel eller felaktiga begär Anden.
* Åtgärder på den hanterade HSM: t. ex. att skapa, ta bort och uppdatera attribut, t. ex. taggar.
* Säkerhets domän relaterade åtgärder som att initiera & Ladda ned, initiera återställning, ladda upp
* Fullständig HSM-säkerhetskopiering, återställning och selektiva återställnings åtgärder
* Åtgärder på nycklar, inklusive:
  * Skapa, ändra eller ta bort nycklar.
  * Signering, verifiering, kryptering, dekryptering, wrapping och unwrap-nycklar, list nycklar.
  * Nyckel säkerhets kopiering, återställning, rensning
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begär Anden som inte har en Bearer-token, som har fel format eller som har upphört att gälla eller som har en ogiltig token.  

## <a name="access-your-logs"></a>Komma åt loggarna

Hanterade HSM-loggar lagras i behållaren **Insights-logs-auditevent** i det lagrings konto som du har angett. Om du vill visa loggarna måste du ladda ned blobbar. Information om Azure Storage finns i [skapa, ladda ned och lista blobar med Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

Enskilda blobbar lagras som text, formaterade som JSON. Nu ska vi titta på en exempel logg post. Exemplet nedan visar logg posten när en begäran om att skapa en fullständig säkerhets kopia skickas till den hanterade HSM: en.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

I följande tabell visas fält namn och beskrivningar:

| Fältnamn | Description |
| --- | --- |
| **TenantId** | Azure Active Directory klient-ID för prenumeration där hanterad HSM skapas |
| **tid** |Datum och tid i UTC. |
| **resourceId** |Azure Resource Manager resurs-ID. För hanterade HSM-loggar är detta alltid det hanterade HSM-resurs-ID: t. |
| **operationName** |Namnet på åtgärden, som beskrivs i nästa tabell. |
| **operationVersion** |REST API version som begärs av klienten. |
| **kategori** |Typ av resultat. **AuditEvent** är det enda tillgängliga värdet för hanterade HSM-loggar. |
| **resultType** |Resultat av den REST API begäran. |
| **egenskaperna** |Information som varierar beroende på åtgärd (**operationName**)|
| **resultSignature** |HTTP-status. |
| **resultDescription** |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| **. Durationms** |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| **callerIpAddress** |IP-adressen för den klient som gjorde begäran. |
| **correlationId** |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klient sidan med loggar på tjänst sidan. |
| **Autentiseringsidentitet** |Identitet från den token som angavs i REST API begäran. Detta är vanligt vis "användare", "tjänstens huvud namn". |
| **requestUri** | URI för REST API begäran |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor loggar för att granska hanterade HSM **AuditEvent** -loggar. I Azure Monitor loggar använder du logg frågor för att analysera data och få den information du behöver. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [metod tips](best-practices.md) för att etablera och använda en HANTERAd HSM
- Läs om [hur du säkerhetskopierar och återställer](backup-restore.md) en HANTERAd HSM
