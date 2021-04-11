---
title: Bevilja behörighet till program för att få åtkomst till ett Azure Key Vault med hjälp av Azure RBAC | Microsoft Docs
description: Lär dig hur du ger åtkomst till nycklar, hemligheter och certifikat med hjälp av rollbaserad åtkomst kontroll i Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216df0d128e0557345db8f82f6010e1ef681593c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968789"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure

> [!NOTE]
> Key Vault Resource provider stöder två resurs typer: **valv** och **hanterade HSM: er**. Åtkomst kontroll som beskrivs i den här artikeln gäller endast **valv**. Mer information om åtkomst kontroll för hanterad HSM finns i [hanterad HSM Access Control](../managed-hsm/access-control.md).

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) är ett auktoriserings system som bygger på [Azure Resource Manager](../../azure-resource-manager/management/overview.md) och ger detaljerad åtkomst hantering av Azure-resurser.

Med Azure RBAC kan användare hantera nyckel-, hemligheter-och certifikat behörigheter. Det ger en plats för att hantera alla behörigheter för alla nyckel valv. 

Azure RBAC-modellen ger möjlighet att ange behörigheter för olika omfattnings nivåer: hanterings grupp, prenumeration, resurs grupp eller enskilda resurser.  Azure RBAC för Key Vault ger också möjlighet att ha separata behörigheter för enskilda nycklar, hemligheter och certifikat

Mer information finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Metod tips för enskilda nycklar, hemligheter och certifikat

Vår rekommendation är att använda ett valv per program per miljö (utveckling, för produktion och produktion).

Behörigheter för enskilda nycklar, hemligheter och certifikat ska endast användas för specifika scenarier:

-   Program med flera lager som behöver separera åtkomst kontroll mellan lager

-   Dela en individuell hemlighet mellan flera program

Mer information om rikt linjer för Azure Key Vault hantering finns i:

- [Översikt över Azure Key Vault säkerhet](security-overview.md)
- [Azure Key Vault tjänst gränser](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Inbyggda Azure-roller för Key Vault data Plans åtgärder
> [!NOTE]
> `Key Vault Contributor` rollen är för hanterings Plans åtgärder för att hantera nyckel valv. Den tillåter inte åtkomst till nycklar, hemligheter och certifikat.

| Inbyggd roll | Description | ID |
| --- | --- | --- |
| Key Vault administratör| Utför alla data Plans åtgärder på ett nyckel valv och alla objekt, inklusive certifikat, nycklar och hemligheter. Det går inte att hantera Key Vault-resurser eller hantera roll tilldelningar. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault certifikats ansvarig | Utföra alla åtgärder för certifikaten för ett nyckel valv, förutom hantera behörigheter. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault kryptografi utanordnare | Utföra alla åtgärder för nycklarna i ett nyckel valv, förutom hantera behörigheter. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Krypterings användare för Key Vault krypterings tjänsten | Läs metadata för nycklar och utför figursatta/unwrap-åtgärder. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault krypto-användare  | Utföra kryptografiska åtgärder med hjälp av nycklar. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault läsare | Läs metadata för nyckel valv och dess certifikat, nycklar och hemligheter. Det går inte att läsa känsliga värden, till exempel hemligt innehåll eller nyckel material. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault hemligheter| Utföra alla åtgärder för ett nyckel valvs hemligheter, förutom hantera behörigheter. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Användare med Key Vault hemligheter | Läsa hemligt innehåll. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 4633458b-17de-408a-b874-0445c86b69e6 |

Mer information om definitioner av inbyggda Azure-roller finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Använda Azure RBAC-hemlighet, nyckel och certifikat behörigheter med Key Vault

Den nya Azure RBAC-behörighets modellen för Key Vault ger ett alternativ till åtkomst princips modellen för valv. 

### <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till roll tilldelningar måste du ha:

- Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) eller [ägare](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Aktivera Azure RBAC-behörigheter på Key Vault

> [!NOTE]
> Ändring av behörighets modellen kräver behörigheten Microsoft. Authorization/roleAssignments/Write, som är en del av [Administratörs](../../role-based-access-control/built-in-roles.md#user-access-administrator) rollerna [ägare](../../role-based-access-control/built-in-roles.md#owner) och användar åtkomst. Administratörs rollen för klassiska prenumerationer som "tjänst administratör" och "co-Administrator" stöds inte.

1.  Aktivera Azure RBAC-behörigheter för nya nyckel valv:

    ![Aktivera Azure RBAC-behörigheter – nytt valv](../media/rbac/image-1.png)

2.  Aktivera Azure RBAC-behörigheter för befintligt nyckel valv:

    ![Aktivera Azure RBAC-behörigheter – befintligt valv](../media/rbac/image-2.png)

> [!IMPORTANT]
> Om du ställer in Azure RBAC-behörighets modell inaktive ras alla behörigheter för åtkomst principer. Det kan orsaka avbrott när motsvarande Azure-roller inte är tilldelade.

### <a name="assign-role"></a>Tilldela rollen

> [!Note]
> Vi rekommenderar att du använder det unika roll-ID: t i stället för roll namnet i skript. Därför fortsätter skripten att fungera om en roll får ett nytt namn. I detta dokument roll namn används endast för läsbarhet.

Kör följande kommando för att skapa en roll tilldelning:

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

På den Azure Portal är skärmen roll tilldelningar för Azure tillgängligt för alla resurser på fliken åtkomst kontroll (IAM).

![Roll tilldelning – fliken (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Roll tilldelning för resurs gruppens omfattning

1.  Gå till resurs gruppen Key Vault.
    ![Roll tilldelning-resurs grupp](../media/rbac/image-4.png)

2.  Klicka på åtkomst kontroll (IAM) \> Lägg till roll tilldelning \> Lägg till

3.  Skapa Key Vault läsar rollen Key Vault läsare för den aktuella användaren

    ![Lägg till roll resurs grupp](../media/rbac/image-5.png)

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

Över roll tilldelningen ger möjlighet att lista Key Vault-objekt i Key Vault.

### <a name="key-vault-scope-role-assignment"></a>Roll tilldelning för Key Vault omfattning

1. Gå till \> fliken Key Vault åtkomst kontroll (IAM)

2. Klicka på Lägg till roll tilldelning \> Lägg till

3. Skapa rollen som administratör för nyckel hemligheter "Key Vault hemligheter" för den aktuella användaren.

    ![Roll tilldelning – nyckel valv](../media/rbac/image-6.png)

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

När du har skapat roll tilldelningen ovan kan du skapa/uppdatera/ta bort hemligheter.

4. Skapa ny hemlighet (hemligheter \> + generate/import) för att testa roll tilldelningen på hemliga nivån.

    ![Lägg till roll nyckel valv](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Roll tilldelning för hemlig omfattning

1. Öppna en av tidigare skapade hemligheter, meddelande översikt och åtkomst kontroll (IAM) 

2. Klicka på fliken åtkomst kontroll (IAM)

    ![Roll tilldelning – hemlighet](../media/rbac/image-8.png)

3. Skapa rollen som administratör för nyckel hemligheter "Key Vault hemligheter" för den aktuella användaren, samma som den gjorde ovan för Key Vault.

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
> Webbläsare använder cachelagring och sid uppdatering krävs efter borttagning av roll tilldelningar.<br>
> Tillåt flera minuter för roll tilldelningar att uppdateras

1. Verifiera att lägga till ny hemlighet utan rollen "Key Vault hemligheter" på nyckel valvs nivå.

Gå till fliken nyckel valv åtkomst kontroll (IAM) och ta bort roll tilldelningen Key Vault hemligheter för den här resursen.

![Ta bort tilldelning – nyckel valv](../media/rbac/image-9.png)

Navigera till tidigare skapade hemligheter. Du kan se alla hemliga egenskaper.

![Hemlig vy med åtkomst](../media/rbac/image-10.png)

Create New Secret (hemligheter \> + generate/import) ska visa följande fel:

   ![Skapa ny hemlighet](../media/rbac/image-11.png)

2.  Verifiera hemlighet redigering utan rollen "Key Vault hemlig tjänsteman" på nivån SECRET.

-   Gå till den tidigare skapade fliken Secret Access Control (IAM) och ta bort roll tilldelningen "Key Vault hemligheter" för den här resursen.

-   Navigera till tidigare skapade hemligheter. Du kan se hemliga egenskaper.

   ![Hemlig vy utan åtkomst](../media/rbac/image-12.png)

3. Verifiera hemligheter Läs utan läsar roll på Key Vault-nivå.

-   Gå till Key Vault resurs gruppens åtkomst kontroll (IAM) och ta bort roll tilldelningen Key Vault läsare.

-   Om du navigerar till Key Vault-fliken hemligheter visas följande fel:

   ![Fliken hemlighet-fel](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Skapa anpassade roller 

[kommandot AZ Role definition Create](/cli/azure/role/definition#az-role-definition-create)

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

## <a name="known-limits-and-performance"></a>Kända begränsningar och prestanda

-   2000 Azure-roll tilldelningar per prenumeration

-   Svars tid för roll tilldelningar: vid aktuell förväntad prestanda tar det upp till 10 minuter (600 sekunder) efter att roll tilldelningarna har ändrats för att rollen ska tillämpas

## <a name="learn-more"></a>Läs mer

- [Översikt över Azure RBAC](../../role-based-access-control/overview.md)
- [Själv studie kurs om anpassade roller](../../role-based-access-control/tutorial-custom-role-cli.md)