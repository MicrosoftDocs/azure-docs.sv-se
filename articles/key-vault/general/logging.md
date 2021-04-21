---
title: Azure Key Vault loggning | Microsoft Docs
description: Lär dig hur du övervakar åtkomsten till dina nyckelvalv genom att aktivera loggning för Azure Key Vault, vilket sparar information i ett Azure Storage-konto som du anger.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5847fcb2cf553e1fcc744877e52dbbdf1f24d992
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751840"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning

När du har skapat ett eller flera nyckelvalv vill du förmodligen övervaka hur och när dina nyckelvalv används och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, vilket sparar information i ett Azure Storage-konto som du anger. Stegvisa anvisningar för hur du ställer in detta finns i [Så här aktiverar du Key Vault loggning.](howto-logging.md)

Du kan komma åt din loggningsinformation 10 minuter (högst) efter nyckelvalvsåtgärden. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna på ditt lagringskonto:

* Använd standardmetoder för Azure-åtkomstkontroll i ditt lagringskonto för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte längre vill behålla på ditt lagringskonto.

Översiktsinformation om Key Vault finns i [Vad är Azure Key Vault?](overview.md). Information om var Key Vault är tillgänglig finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/key-vault/). Information om hur du [använder Azure Monitor för Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Tolka Key Vault-loggarna

När du aktiverar loggning skapas automatiskt en ny container med namnet **insights-logs-auditevent** för ditt angivna lagringskonto. Du kan använda samma lagringskonto för att samla in loggar för flera nyckelvalv.

Enskilda blobbar lagras som text, formaterad som en JSON-blobb. Nu ska vi titta på en exempelloggpost. 

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

I följande tabell visas fältnamn och beskrivningar:

| Fältnamn | Description |
| --- | --- |
| **Tid** |Datum och tid i UTC. |
| **resourceId** |Azure Resource Manager resurs-ID. För Key Vault-loggar är detta alltid det Key Vault resurs-ID:t. |
| **operationName** |Namnet på åtgärden, som beskrivs i nästa tabell. |
| **operationVersion** |REST API-version som begärs av klienten. |
| **Kategori** |Typ av resultat. För Key Vault-loggar `AuditEvent` är det enda, tillgängliga värdet. |
| **resultType** |Resultatet av REST API begäran. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| **durationMs** |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| **callerIpAddress** |IP-adressen för klienten som gjorde begäran. |
| **correlationId** |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| **Identitet** |Identitet från den token som visades i REST API begäran. Detta är vanligtvis en "användare", ett "tjänstens huvudnamn" eller kombinationen "user+appId", som i fallet med en begäran som kommer från en Azure PowerShell cmdlet. |
| **Egenskaper** |Information som varierar beroende på åtgärden (**operationName**). I de flesta fall innehåller det här fältet klientinformation (användarens agentsträng som skickas av klienten), den exakta REST API-begärande-URI:n och HTTP-statuskoden. När ett objekt returneras som ett resultat av en begäran (till exempel **KeyCreate** eller **VaultGet)** innehåller det dessutom nyckel-URI :n (som ), valvets URI eller hemliga `id` URI. |

**OperationName-fältvärdena** är i *ObjectVerb-format.* Exempel:

* Alla nyckelvalvsåtgärder har `Vault<action>` formatet , till exempel och `VaultGet` `VaultCreate` .
* Alla nyckelåtgärder har `Key<action>` formatet, till exempel `KeySign` och `KeyList` .
* Alla hemliga åtgärder har `Secret<action>` formatet , till exempel och `SecretGet` `SecretListVersions` .

I följande tabell visas **operationName-värden** och motsvarande REST API kommandon:

### <a name="operation-names-table"></a>Tabell med åtgärdsnamn

# <a name="vault"></a>[Valv](#tab/Vault)

| operationName | REST API kommando |
| --- | --- |
| **Autentisering** |Autentisera via Azure Active Directory slutpunkt |
| **VaultGet** |[Hämta information om ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultPut** |[Skapa eller uppdatera ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Ta bort ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Uppdatera ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultList** |[Visa en lista med alla nyckelvalv i en resursgrupp](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Rensa borttagna valv](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Återställa borttagna valv|
| **VaultGetDeleted** |[Hämta borttagna valv](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Lista borttagna valv](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Händelse om ändrad händelse för valvåtkomstprincip publicerades |

# <a name="keys"></a>[Nycklar](#tab/Keys)

| operationName | REST API kommando |
| --- | --- |
| **KeyCreate** |[Skapa en nyckel](/rest/api/keyvault/createkey) |
| **KeyGet** |[Hämta information om en nyckel](/rest/api/keyvault/getkey) |
| **KeyImport** |[Importera en nyckel till ett valv](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Ta bort en nyckel](/rest/api/keyvault/deletekey) |
| **KeySign** |[Signera med en nyckel](/rest/api/keyvault/sign) |
| **KeyVerify** |[Verifiera med en nyckel](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Omsluta en nyckel](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Ta bort en nyckelomslutning](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Kryptera med en nyckel](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Dekryptera med en nyckel](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Uppdatera en nyckel](/rest/api/keyvault/updatekey) |
| **KeyList** |[Visa en lista med nycklarna i ett valv](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Visa en lista över versionerna av en nyckel](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[Rensa en nyckel](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Säkerhetskopiera en nyckel](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Återställa en nyckel](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[Återställa en nyckel](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Hämta borttagna nycklar](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Lista de borttagna nycklarna i ett valv](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Nyckel som snart upphör att gälla publiceras |
| **KeyExpiredEventGridNotification** |Publicering av händelse som har upphört att gälla för nyckeln |

# <a name="secrets"></a>[Hemligheter](#tab/Secrets)

| operationName | REST API kommando |
| --- | --- |
| **SecretSet** |[Skapa en hemlighet](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Hämta en hemlighet](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Uppdatera en hemlighet](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Ta bort en hemlighet](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Visa en lista över hemligheterna i ett valv](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Visa en lista över versionerna av en hemlighet](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Rensa en hemlighet](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Säkerhetskopiera en hemlighet](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Återställa en hemlighet](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Återställa en hemlighet](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Hämta borttagna hemligheter](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Visa en lista över de borttagna hemligheterna i ett valv](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Hemlig händelse som snart upphör att gälla publiceras |
| **SecretExpiredEventGridNotification** |Hemlig händelse som har upphört att gälla publiceras |

# <a name="certificates"></a>[Certifikat](#tab/Cerificates)

| operationName | REST API kommando |
| --- | --- |
| **CertificateGet** |[Hämta information om ett certifikat](/rest/api/keyvault/getcertificate) |
| **CertifikatSkapa** |[Skapa ett certifikat](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Importera ett certifikat till ett valv](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Uppdatera ett certifikat](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[Lista certifikaten i ett valv](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[Visa en lista över versioner av ett certifikat](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Ta bort ett certifikat](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Rensa ett certifikat](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Säkerhetskopiera ett certifikat](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Återställa ett certifikat](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Återställa ett certifikat](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Hämta borttagna certifikat](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Lista de borttagna certifikaten i ett valv](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Hämta certifikatprincip](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Uppdatera certifikatprincip](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Skapa certifikatprincip](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Hämta certifikatkontakter](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Ange certifikatkontakter](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Ta bort certifikatkontakter](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Hämta certifikatutfärdare](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Ange certifikatutfärdare](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Uppdatera certifikatutfärdare](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Ta bort certifikatutfärdare](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[Lista certifikatutfärdare](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Registrera ett certifikat |
| **CertificateRenew** |Förnya ett certifikat |
| **CertificatePendingGet** |Hämta väntande certifikat |
| **CertificatePendingMerge** |Väntande en certifikatsammanslagning |
| **CertificatePendingUpdate** |Väntar på en certifikatuppdatering |
| **CertificatePendingDelete** |Ta bort väntande certifikat |
| **CertificateNearExpiryEventGridNotification** |Certifikat som snart upphör att gälla publiceras |
| **CertificateExpiredEventGridNotification** |Certifikatet har publicerat en händelse som har upphört att gälla |

---

## <a name="use-azure-monitor-logs"></a>Använda Azure Monitor-loggar

Du kan använda lösningen Key Vault i Azure Monitor för att granska Key Vault `AuditEvent` loggar. I Azure Monitor loggar använder du loggfrågor för att analysera data och hämta den information du behöver. 

Mer information, inklusive hur du ställer in detta, finns [i Azure Key Vault i Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Så här aktiverar du Key Vault loggning](howto-logging.md)
- [Azure Monitor](../../azure-monitor/index.yml)
- En självstudiekurs som använder Azure Key Vault i en .NET-webbapp finns i [Använda Azure Key Vault från en webbapp](tutorial-net-create-vault-azure-web-app.md).
- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](developers-guide.md).
- En lista över Azure PowerShell 1,0 cmdlets för Azure Key Vault finns i [Azure Key Vault cmdlets](/powershell/module/az.keyvault/#key_vault).