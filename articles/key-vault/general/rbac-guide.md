---
title: Bevilja behörighet till program för att få åtkomst till ett Azure-nyckelvalv med hjälp av Azure RBAC | Microsoft Docs
description: Lär dig hur du ger åtkomst till nycklar, hemligheter och certifikat med hjälp av rollbaserad åtkomstkontroll i Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 966f704bd47b4b238ed72579a6103bd2e4348849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772225"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Ge åtkomst till Key Vault, certifikat och hemligheter med en rollbaserad åtkomstkontroll i Azure

> [!NOTE]
> Key Vault har stöd för två resurstyper: **valv och** **hanterade HSM:er.** Åtkomstkontroll som beskrivs i den här artikeln gäller endast **för valv**. Mer information om åtkomstkontroll för hanterad HSM finns i [Hanterad HSM-åtkomstkontroll.](../managed-hsm/access-control.md)

Rollbaserad åtkomstkontroll i Azure (Azure RBAC) är ett auktoriseringssystem som [bygger på Azure Resource Manager](../../azure-resource-manager/management/overview.md) som ger en mer fullständig åtkomsthantering av Azure-resurser.

Med Azure RBAC kan användare hantera behörigheter för nyckel, hemligheter och certifikat. Det ger en plats för att hantera alla behörigheter i alla nyckelvalv. 

Azure RBAC-modellen ger möjlighet att ange behörigheter på olika omfångsnivåer: hanteringsgrupp, prenumeration, resursgrupp eller enskilda resurser.  Azure RBAC för nyckelvalv ger också möjlighet att ha separata behörigheter för enskilda nycklar, hemligheter och certifikat

Mer information finns i [Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/overview.md)

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Metodtips för enskilda nycklar, hemligheter och certifikat

Vår rekommendation är att använda ett valv per program per miljö (utveckling, förproduktion och produktion).

Enskilda nycklar, hemligheter och certifikatbehörigheter bör endast användas för specifika scenarier:

-   Flerskiktsprogram som behöver separera åtkomstkontroll mellan lager

-   Dela en enskild hemlighet mellan flera program

Mer information Azure Key Vault riktlinjer för hantering finns i:

- [Azure Key Vault säkerhetsöversikt](security-overview.md)
- [Azure Key Vault tjänstbegränsningar](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Inbyggda Roller i Azure för Key Vault åtgärder på dataplanet
> [!NOTE]
> `Key Vault Contributor` rollen är för hanteringsplanåtgärder för att hantera nyckelvalv. Den tillåter inte åtkomst till nycklar, hemligheter och certifikat.

| Inbyggd roll | Description | ID |
| --- | --- | --- |
| Key Vault administratör| Utför alla dataplansåtgärder på ett nyckelvalv och alla objekt i det, inklusive certifikat, nycklar och hemligheter. Det går inte att hantera nyckelvalvsresurser eller rolltilldelningar. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault certifikatansvarig | Utför en åtgärd på certifikaten för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer | Utför en åtgärd på nycklarna för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen rollbaserad åtkomstkontroll i Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault Crypto Service Encryption User | Läs metadata för nycklar och utför radbrytningsåtgärder. Fungerar bara för nyckelvalv som använder behörighetsmodellen rollbaserad åtkomstkontroll i Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault Crypto-användare  | Utföra kryptografiska åtgärder med hjälp av nycklar. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault Läsare | Läs metadata för nyckelvalv och dess certifikat, nycklar och hemligheter. Det går inte att läsa känsliga värden, till exempel hemligt innehåll eller nyckelmaterial. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer| Utför en åtgärd på hemligheterna för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault hemlighetsanvändare | Läsa hemligt innehåll. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |

Mer information om inbyggda rolldefinitioner i Azure finns i [Inbyggda roller i Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Använda Azure RBAC-behörigheter för hemlighet, nyckel och certifikat med Key Vault

Den nya Azure RBAC-behörighetsmodellen för nyckelvalvet är ett alternativ till behörighetsmodellen för valvåtkomstprincip. 

### <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till rolltilldelningar måste du ha:

- Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) eller [Ägare](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Aktivera Azure RBAC-behörigheter på Key Vault

> [!NOTE]
> För att ändra behörighetsmodellen krävs behörigheten Microsoft.Authorization/roleAssignments/write, som är en del av [rollerna Ägare](../../role-based-access-control/built-in-roles.md#owner) och [Administratör för användaråtkomst.](../../role-based-access-control/built-in-roles.md#user-access-administrator) Administratörsroller för klassiska prenumerationer som Tjänstadministratör och Medadministratör stöds inte.

1.  Aktivera Azure RBAC-behörigheter för nytt nyckelvalv:

    ![Aktivera Azure RBAC-behörigheter – nytt valv](../media/rbac/image-1.png)

2.  Aktivera Azure RBAC-behörigheter för befintligt nyckelvalv:

    ![Aktivera Azure RBAC-behörigheter – befintligt valv](../media/rbac/image-2.png)

> [!IMPORTANT]
> Om du anger Azure RBAC-behörighetsmodellen ogiltigförklaras alla behörigheter för åtkomstprinciper. Det kan orsaka avbrott när motsvarande Azure-roller inte har tilldelats.

### <a name="assign-role"></a>Tilldela rollen

> [!Note]
> Vi rekommenderar att du använder det unika roll-ID:t i stället för rollnamnet i skript. Om en roll byter namn fortsätter därför skripten att fungera. I det här dokumentet används rollnamnet endast för läsbarhet.

Kör följande kommando för att skapa en rolltilldelning:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

På Azure Portal skärmen för Azure-rolltilldelningar tillgänglig för alla resurser på fliken Åtkomstkontroll (IAM).

![Rolltilldelning – fliken (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Rolltilldelning för resursgruppsomfång

1.  Gå till Resursgrupp för nyckelvalv.
    ![Rolltilldelning – resursgrupp](../media/rbac/image-4.png)

2.  Klicka på Åtkomstkontroll (IAM) \> Lägg till \> rolltilldelning

3.  Skapa Key Vault läsarrollen "Key Vault Reader" för den aktuella användaren

    ![Lägg till roll – resursgrupp](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

Ovanstående rolltilldelning ger möjlighet att lista nyckelvalvsobjekt i nyckelvalvet.

### <a name="key-vault-scope-role-assignment"></a>Key Vault tilldelning av omfångsroll

1. Gå till Key Vault \> för åtkomstkontroll (IAM)

2. Klicka på Lägg till rolltilldelning Lägg \> till

3. Skapa key secrets officer-rollen "Key Vault Secrets Officer" för den aktuella användaren.

    ![Rolltilldelning – nyckelvalv](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

När du har skapat rolltilldelningen ovan kan du skapa/uppdatera/ta bort hemligheter.

4. Skapa ny hemlighet ( Hemligheter \> +Generera/importera) för att testa rolltilldelning på hemlighetsnivå.

    ![Lägg till roll – nyckelvalv](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Rolltilldelning för hemlighetsomfång

1. Öppna en av tidigare skapade hemligheter, lägg märke till Översikt och Åtkomstkontroll (IAM) 

2. Klicka på fliken Åtkomstkontroll (IAM)

    ![Rolltilldelning – hemlighet](../media/rbac/image-8.png)

3. Skapa key secrets officer-rollen "Key Vault Secrets Officer" för den aktuella användaren, på samma sätt som den gjordes ovan för Key Vault.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>Testa och verifiera

> [!NOTE]
> Webbläsare använder cachelagring och siduppdatering krävs efter borttagning av rolltilldelningar.<br>
> Det kan ta flera minuter för rolltilldelningar att uppdateras

1. Validera tillägg av ny hemlighet utan rollen "Key Vault Secrets Officer" på nyckelvalvsnivå.

Gå till fliken Åtkomstkontroll för nyckelvalv (IAM) och ta bort rolltilldelningen "Key Vault Secrets Officer" för den här resursen.

![Ta bort tilldelning – nyckelvalv](../media/rbac/image-9.png)

Gå till hemligheten du skapade tidigare. Du kan se alla hemliga egenskaper.

![Hemlig vy med åtkomst](../media/rbac/image-10.png)

Skapa ny hemlighet ( Hemligheter \> +Generera/Importera) bör visas nedan:

   ![Skapa ny hemlighet](../media/rbac/image-11.png)

2.  Validera redigering av hemligheter utan att Key Vault rollen "Hemlig handläggare" på hemlighetsnivå.

-   Gå till fliken Secret Access Control (IAM) som du skapade tidigare och ta bort rolltilldelningen "Key Vault Secrets Officer" för den här resursen.

-   Gå till hemligheten du skapade tidigare. Du kan se hemliga egenskaper.

   ![Hemlig vy utan åtkomst](../media/rbac/image-12.png)

3. Validera läsning av hemligheter utan läsarroll på nyckelvalvsnivå.

-   Gå till fliken Åtkomstkontroll (IAM) för nyckelvalvsresursgruppen och ta bort rolltilldelningen "Key Vault Läsare".

-   Om du går till fliken Hemligheter i nyckelvalvet bör felet nedan visas:

   ![Fliken Hemlighet – fel](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Skapa anpassade roller 

[az role definition create command](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Mer information om hur du skapar anpassade roller finns i:

[Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Kända gränser och prestanda

-   2 000 Azure-rolltilldelningar per prenumeration

-   Svarstid för rolltilldelningar: vid aktuell förväntad prestanda tar det upp till 10 minuter (600 sekunder) efter att rolltilldelningar har ändrats för att rollen ska tillämpas

## <a name="learn-more"></a>Läs mer

- [Översikt över Azure RBAC](../../role-based-access-control/overview.md)
- [Självstudie om anpassade roller](../../role-based-access-control/tutorial-custom-role-cli.md)