---
title: Distribuera en princip som kan åtgärdas
description: Om du vill distribuera principer som använder en reparations uppgift via Azure Lighthouse måste du skapa en hanterad identitet i kund klienten.
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233890"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Distribuera en princip som kan åtgärdas inom en delegerad prenumeration

Med [Azure-Lighthouse](../overview.md) kan tjänst leverantörer skapa och redigera princip definitioner i en delegerad prenumeration. Men om du vill distribuera principer som använder en [reparations uppgift](../../governance/policy/how-to/remediate-resources.md) (det vill säga principer med [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) eller [ändra](../../governance/policy/concepts/effects.md#modify) -effekter) måste du skapa en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) i kund klienten. Den här hanterade identiteten kan användas av Azure Policy för att distribuera mallen i principen. Det finns åtgärder som krävs för att aktivera det här scenariot, både när du registrerar kunden för Azure-delegerad resurs hantering och när du distribuerar själva principen.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet kan [företag som hanterar flera klienter](../concepts/enterprise.md) använda samma processer.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Skapa en användare som kan tilldela roller till en hanterad identitet i kund klienten

När du registrerar en kund i Azure Lighthouse, använder du en [Azure Resource Manager-mall](onboard-customer.md#create-an-azure-resource-manager-template) tillsammans med en parameter fil för att definiera auktoriseringar som beviljar åtkomst till delegerade resurser i kund klienten. Varje auktorisering anger en **principalId** som motsvarar en Azure AD-användare,-grupp eller-tjänstens huvud namn i hanterings klienten och en **roleDefinitionId** som motsvarar den [inbyggda Azure-rollen](../../role-based-access-control/built-in-roles.md) som kommer att beviljas.

Om du vill tillåta en **principalId** att skapa en hanterad identitet i kundens klient organisation måste du ange dess **RoleDefinitionId** till **användar åtkomst administratör**. Även om den här rollen inte stöds i allmänhet, kan den användas i det här scenariot, vilket gör det möjligt för användar konton med behörighet att tilldela en eller flera angivna inbyggda roller till hanterade identiteter. De här rollerna definieras i egenskapen **delegatedRoleDefinitionIds** och kan innehålla en [inbyggd Azure-inbyggd roll som stöds](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) , förutom administratör för användar åtkomst eller ägare.

När kunden har registrerats kommer **principalId** som skapats i det här tillståndet att kunna tilldela de inbyggda rollerna till hanterade identiteter i kund klienten. De kommer dock inte att ha några andra behörigheter som vanligt vis är associerade med rollen administratör för användar åtkomst.

Exemplet nedan visar en **principalId** som kommer att ha rollen administratör för användar åtkomst. Den här användaren kan tilldela två inbyggda roller till hanterade identiteter i kund klienten: bidrags givare och Log Analytics deltagare.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Distribuera principer som kan åtgärdas

När du har skapat användaren med nödvändiga behörigheter enligt beskrivningen ovan, kan den användaren distribuera principer som använder reparations uppgifter i delegerade kund prenumerationer.

Anta till exempel att du vill aktivera diagnostik på Azure Key Vault resurser i kund klienten, som du ser i det här [exemplet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). En användare i hanterings klienten med rätt behörigheter (enligt beskrivningen ovan) distribuerar en Azure Resource Manager- [mall](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) för att aktivera det här scenariot.

Observera att när du skapar princip tilldelningen som ska användas med en delegerad prenumeration måste den för närvarande utföras via API: er, inte i Azure Portal. När du gör det måste **API version** anges till **2019-04-01-Preview**, som innehåller den nya **delegatedManagedIdentityResourceId** -egenskapen. Med den här egenskapen kan du ta med en hanterad identitet som finns i kund klient organisationen (i en prenumeration eller resurs grupp som har publicerats till Azure Lighthouse).

I följande exempel visas en roll tilldelning med en **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Ett [liknande exempel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) är tillgängligt för att demonstrera hur du distribuerar en princip som lägger till eller tar bort en tagg (med hjälp av ändra-effekter) till en delegerad prenumeration.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure policy](../../governance/policy/index.yml).
- Lär dig mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).
