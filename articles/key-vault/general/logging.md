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
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: d900659f3ca8a8688c1b1d3a66cd888f37521fc6
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883392"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning

När du har skapat ett eller flera nyckel valv vill du förmodligen övervaka hur och när nyckel valven nås, och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, som sparar information i ett Azure Storage-konto som du anger. Steg för steg-anvisningar om hur du konfigurerar detta finns i [så här aktiverar du Key Vault loggning](howto-logging.md).

Du kan komma åt loggnings informationen 10 minuter (högst) efter nyckel valvs åtgärden. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd Azures standard metoder för åtkomst kontroll i ditt lagrings konto för att skydda dina loggar genom att begränsa vem som har åtkomst till dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Översiktlig information om Key Vault finns i [Vad är Azure Key Vault?](overview.md). Information om var Key Vault finns på [sidan med priser](https://azure.microsoft.com/pricing/details/key-vault/). Information om hur du använder [Azure Monitor för Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Tolka Key Vault-loggarna

När du aktiverar loggning skapas en ny behållare med namnet **Insights-logs-auditevent** automatiskt för det angivna lagrings kontot. Du kan använda samma lagrings konto för att samla in loggar för flera nyckel valv.

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

Värdena för **operationName** -fältet är i *ObjectVerb* -format. Till exempel:

* Alla Key Vault-åtgärder har `Vault<action>` formatet, till exempel `VaultGet` och `VaultCreate` .
* Alla viktiga åtgärder har `Key<action>` formatet, till exempel `KeySign` och `KeyList` .
* Alla hemliga åtgärder har `Secret<action>` formatet, till exempel `SecretGet` och `SecretListVersions` .

I följande tabell visas **operationName** -värdena och motsvarande REST API-kommandon:

### <a name="operation-names-table"></a>Åtgärds namn tabell

# <a name="vault"></a>[Valv](#tab/Vault)

| operationName | REST API kommando |
| --- | --- |
| **Autentisering** |Autentisera via Azure Active Directory slut punkt |
| **VaultGet** |[Hämta information om ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultPut** |[Skapa eller uppdatera ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Ta bort ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Uppdatera ett nyckelvalv](/rest/api/keyvault/vaults) |
| **VaultList** |[Visa en lista med alla nyckelvalv i en resursgrupp](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Ta bort borttaget valv](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Återställ borttaget valv|
| **VaultGetDeleted** |[Hämta borttaget valv](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Lista borttagna valv](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Händelse publicering har ändrats för valv åtkomst princip |

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
| **Rensa och rensa** |[Rensa en nyckel](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Säkerhetskopiera en nyckel](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Återställa en nyckel](/rest/api/keyvault/restorekey) |
| **Återställ återställning** |[Återställa en nyckel](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Hämta borttagen nyckel](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Lista de borttagna nycklarna i ett valv](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Nyckel för nära förfallo händelser publicerad |
| **KeyExpiredEventGridNotification** |Händelse publicerad för nyckel utgångna |

# <a name="secrets"></a>[Hemligheter](#tab/Secrets)

| operationName | REST API kommando |
| --- | --- |
| **SecretSet** |[Skapa en hemlighet](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Få en hemlighet](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Uppdatera en hemlighet](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Ta bort en hemlighet](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Visa en lista över hemligheterna i ett valv](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Visa en lista över versionerna av en hemlighet](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Rensa en hemlighet](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Säkerhetskopiera en hemlighet](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Återställa en hemlighet](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Återställa en hemlighet](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Hämta borttagen hemlighet](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Lista de borttagna hemligheterna i ett valv](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Hemlig händelse som är nära utgångs händelse publicerad |
| **SecretExpiredEventGridNotification** |Hemligt utgånget händelse publicerat |

# <a name="certificates"></a>[Certifikat](#tab/Cerificates)

| operationName | REST API kommando |
| --- | --- |
| **CertificateGet** |[Hämta information om ett certifikat](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[Skapa ett certifikat](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Importera ett certifikat till ett valv](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Uppdatera ett certifikat](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[Visa en lista över certifikaten i ett valv](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[Visa en lista över versioner av ett certifikat](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Ta bort ett certifikat](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Rensa ett certifikat](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Säkerhetskopiera ett certifikat](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Återställa ett certifikat](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Återställa ett certifikat](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Hämta borttaget certifikat](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Lista de borttagna certifikaten i ett valv](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Hämta certifikat princip](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Uppdatera certifikat princip](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Skapa certifikat princip](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Hämta certifikat kontakter](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Ange certifikat kontakter](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Ta bort certifikat kontakter](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Hämta certifikat utfärdare](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Ange certifikat utfärdare](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Uppdatera certifikat utfärdare](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Ta bort certifikat utfärdare](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[Visa en lista över certifikat utfärdare](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Registrera ett certifikat |
| **CertificateRenew** |Förnya ett certifikat |
| **CertificatePendingGet** |Hämta väntande certifikat |
| **CertificatePendingMerge** |Väntande certifikat sammanslagning |
| **CertificatePendingUpdate** |Väntande en certifikat uppdatering |
| **CertificatePendingDelete** |Ta bort väntande certifikat |
| **CertificateNearExpiryEventGridNotification** |Certifikat nära utgångs händelse publicerad |
| **CertificateExpiredEventGridNotification** |Utgånget certifikat-händelse publicerat |
---

## <a name="use-azure-monitor-logs"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor loggar för att granska Key Vault `AuditEvent` loggar. I Azure Monitor loggar använder du logg frågor för att analysera data och få den information du behöver. 

Mer information, inklusive hur du konfigurerar detta finns i [Azure Key Vault i Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Så här aktiverar du Key Vault loggning](howto-logging.md)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- En själv studie kurs som använder Azure Key Vault i ett .NET-webb program finns i [använda Azure Key Vault från ett webb program](tutorial-net-create-vault-azure-web-app.md).
- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](developers-guide.md).
- En lista över Azure PowerShell 1,0-cmdletar för Azure Key Vault finns i [Azure Key Vault-cmdletar](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
