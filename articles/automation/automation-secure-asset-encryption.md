---
title: Kryptering av säkra resurser i Azure Automation
description: Den här artikeln innehåller begrepp för att konfigurera Automation-kontot att använda kryptering.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: b46cf3a742158a3347b43a1e9bc6d62d0b2160d4
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773699"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Kryptering av säkra resurser i Azure Automation

Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar skyddas i Azure Automation att använda flera krypterings nivåer. Baserat på den översta nyckeln som används för krypteringen finns det två modeller för kryptering:

- Använda Microsoft-hanterade nycklar
- Använda nycklar som du hanterar

## <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder ditt Azure Automation-konto Microsoft-hanterade nycklar.

Varje säker till gång krypteras och lagras i Azure Automation med hjälp av en unik nyckel (data krypterings nyckel) som genereras för varje Automation-konto. Dessa nycklar krypteras och lagras i Azure Automation att använda ännu en unik nyckel som genereras för varje konto som kallas för en konto krypterings nyckel (AEK). Krypterings nycklarna för kontot krypteras och lagras i Azure Automation med hjälp av Microsoft-hanterade nycklar. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Nycklar som du hanterar med Key Vault (förhands granskning)

Du kan hantera kryptering av säkra till gångar för ditt Automation-konto med dina egna nycklar. När du anger en kundhanterad nyckel på nivån för Automation-kontot används nyckeln för att skydda och kontrol lera åtkomsten till konto krypterings nyckeln för Automation-kontot. Detta används i sin tur för att kryptera och dekryptera alla säkra till gångar. Kundhanterade nycklar ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina säkra till gångar.

Använd Azure Key Vault för att lagra Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar.  Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Användning av Kundhanterade nycklar för ett Automation-konto

När du använder kryptering med Kundhanterade nycklar för ett Automation-konto omsluter Azure Automation konto krypterings nyckeln med den Kundhanterade nyckeln i det associerade nyckel valvet. Att aktivera Kundhanterade nycklar påverkar inte prestanda och kontot krypteras med den nya nyckeln omedelbart utan fördröjning.

Ett nytt Automation-konto krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera Kundhanterade nycklar vid den tidpunkt då kontot skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckel valvet måste tillhandahållas med åtkomst principer som ger nyckel behörigheter till den hanterade identitet som är associerad med Automation-kontot. Den hanterade identiteten är bara tillgänglig när lagrings kontot har skapats.

När du ändrar nyckeln som används för Azure Automation säker till gångs kryptering, genom att aktivera eller inaktivera Kundhanterade nycklar, uppdatera nyckel versionen eller ange en annan nyckel, ändras krypteringen av kontots krypterings nyckel men de skyddade till gångarna i ditt Azure Automation konto behöver inte krypteras på nytt.

> [!NOTE] 
> Om du vill aktivera Kundhanterade nycklar måste du göra Azure Automation REST API samtal med API-version 2020-01-13-för hands version

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Krav för att använda Kundhanterade nycklar i Azure Automation

Innan du aktiverar Kundhanterade nycklar för ett Automation-konto måste du se till att följande krav är uppfyllda:

 - Kund-hanterade-nyckeln lagras i en Azure Key Vault. 
 - Aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet. Dessa funktioner krävs för att kunna återställa nycklar i händelse av oavsiktlig borttagning.
 - Endast RSA-nycklar stöds med Azure Automation kryptering. Mer information om nycklar finns i [om Azure Key Vault nycklar, hemligheter och certifikat](../key-vault/general/about-keys-secrets-certificates.md).
- Automation-kontot och nyckel valvet kan finnas i olika prenumerationer, men måste vara i samma Azure Active Directory-klient.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Tilldelning av en identitet till Automation-kontot

Om du vill använda Kundhanterade nycklar med ett Automation-konto måste ditt Automation-konto autentisera mot nyckel valvet som lagrar Kundhanterade nycklar. Azure Automation använder systemtilldelade hanterade identiteter för att autentisera kontot med Azure Key Vault. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

Konfigurera en systemtilldelad hanterad identitet till Automation-kontot med följande REST API anrop:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Begärandetext:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Tilldelad identitet för Automation-kontot returneras i ett svar som liknar följande:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>Konfiguration av Key Vault åtkomst princip

När en hanterad identitet har tilldelats Automation-kontot konfigurerar du åtkomst till nyckel valvet som lagrar Kundhanterade nycklar. Azure Automation kräver **Get**, **Recover**, **wrapKey**, **UnwrapKey** på kundens hanterade nycklar.

En sådan åtkomst princip kan anges med följande REST API anrop:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Begärandetext:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> **TenantId** -och **ObjectID** -fälten måste anges med värdena **Identity. tenantId** och **Identity. principalId** från svar på hanterad identitet för Automation-kontot.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Ändra konfigurationen för Automation-kontot så att den använder kundhanterad nyckel

Slutligen kan du växla ditt Automation-konto från Microsoft-hanterade nycklar till Kundhanterade nycklar med följande REST API anrop:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Begärandetext:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Exempelsvar

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotation av en kundhanterad nyckel

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera Automation-kontot för att använda den nya nyckel-URI: n.

Rotation av nyckeln utlöser inte Omkryptering av skyddade till gångar i Automation-kontot. Ingen ytterligare åtgärd krävs.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Återkallande av åtkomst till en kundhanterad nyckel

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) eller [Azure Key Vault CLI](/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomst till alla skyddade till gångar i Automation-kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Automation.

## <a name="next-steps"></a>Nästa steg

- Information om Azure Key Vault finns i [Azure Key Vault?](../key-vault/general/overview.md).
- Information om hur du arbetar med certifikat finns [i hantera certifikat i Azure Automation](shared-resources/certificates.md).
- Information om hur du hanterar autentiseringsuppgifter finns [i Hantera autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).
- Om du vill använda Automation-variabler [hanterar du variabler i Azure Automation](shared-resources/variables.md).
- Information om hur du arbetar med anslutningar finns [i hantera anslutningar i Azure Automation](automation-connections.md).
