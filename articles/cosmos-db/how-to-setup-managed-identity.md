---
title: Konfigurera hanterade identiteter med Azure AD för ditt Azure Cosmos DB-konto
description: Lär dig hur du konfigurerar hanterade identiteter med Azure Active Directory för ditt Azure Cosmos DB-konto
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231501"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Konfigurera hanterade identiteter med Azure Active Directory för ditt Azure Cosmos DB-konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Den här artikeln visar hur du skapar en hanterad identitet för Azure Cosmos DB-konton.

> [!NOTE]
> Endast systemtilldelade hanterade identiteter stöds för närvarande av Azure Cosmos DB.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser, se [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md). Information om hanterade identitets typer finns i [hanterade identitets typer](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Om du vill konfigurera hanterade identiteter måste ditt konto ha [rollen DocumentDB Account Contributor](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Lägg till en tilldelad identitet

### <a name="using-an-azure-resource-manager-arm-template"></a>Använda en Azure Resource Manager-mall (ARM)

> [!IMPORTANT]
> Se till att använda en `apiVersion` `2021-03-15` eller högre när du arbetar med hanterade identiteter.

Om du vill aktivera en systemtilldelad identitet på ett nytt eller befintligt Azure Cosmos DB konto, inkluderar du följande egenskap i resurs definitionen:

```json
"identity": {
    "type": "SystemAssigned"
}
```

`resources`Avsnittet i arm-mallen bör sedan se ut så här:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

När ditt Azure Cosmos DB-konto har skapats eller uppdaterats visas följande egenskap:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md)
- Lär dig mer om [Kundhanterade nycklar på Azure Cosmos DB](how-to-setup-cmk.md)
