---
title: Hantera ett kör Azure Automation kör som-konto
description: Den här artikeln beskriver hur du hanterar ditt Kör som-konto med PowerShell eller från Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e440a27c8f7778c800148feb5bec76ca5a48f4f5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833930"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Hantera ett kör Azure Automation kör som-konto

Kör som-konton i Azure Automation autentisering för att hantera resurser på Azure Resource Manager eller den klassiska Azure-distributionsmodellen med hjälp av Automation-runbooks och andra Automation-funktioner. Den här artikeln innehåller råd om hur du hanterar ett Kör som-konto eller ett klassiskt Kör som-konto.

Mer information om hur Azure Automation-kontoautentisering och vägledning för processautomatiseringsscenarier finns i [Översikt över Automation-kontoautentisering.](automation-security-overview.md)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Förnya ett själv signerat certifikat

Det själv signerade certifikatet som du har skapat för Kör som-kontot upphör att gälla ett år från det datum då det skapades. Du måste förnya certifikatet någon gång innan Ditt Kör som-konto upphör att gälla. Du kan förnya den när som helst innan den upphör att gälla.

När du förnyar det själv signerade certifikatet behålls det aktuella giltiga certifikatet för att säkerställa att alla runbooks som är i kö eller körs aktivt och som autentiserar med Kör som-kontot inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

>[!NOTE]
>Om du tror att Kör som-kontot har komprometterats kan du ta bort och skapa det själv signerade certifikatet igen.

>[!NOTE]
>Om du har konfigurerat ditt Kör som-konto att använda ett certifikat som utfärdats av din företagscertifikatutfärdare och du använder alternativet för att förnya ett själv signerat certifikat, ersätts företagscertifikatet med ett själv signerat certifikat.

Använd följande steg för att förnya det själv signerade certifikatet.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till ditt Automation-konto och **välj Kör som-konton** i avsnittet kontoinställningar.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Fönstret Egenskaper för Automation-konto.":::

1. På **egenskapssidan För Kör som-konton** väljer du antingen **Kör** som-konto eller Klassiskt **Kör** som-konto beroende på vilket konto du behöver förnya certifikatet för.

1. På sidan **Egenskaper** för det valda kontot väljer du **Förnya certifikat.**

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Förnya certifikat för Kör som-konto.":::

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Bevilja Kör som-kontobehörigheter i andra prenumerationer

Azure Automation har stöd för att använda ett enda Automation-konto från en prenumeration och köra runbooks mot Azure Resource Manager resurser över flera prenumerationer. Den här konfigurationen stöder inte den klassiska Azure-distributionsmodellen.

Du tilldelar tjänstens huvudnamn för Kör [som-kontot rollen](../role-based-access-control/built-in-roles.md#contributor) Deltagare i den andra prenumerationen eller mer restriktiva behörigheter. Mer information finns i [Rollbaserad åtkomstkontroll i](automation-role-based-access-control.md) Azure Automation. Om du vill tilldela Kör som-kontot till rollen i den andra prenumerationen måste användarkontot som utför den här uppgiften vara medlem i rollen **Ägare** i prenumerationen.

> [!NOTE]
> Den här konfigurationen stöder endast flera prenumerationer i en organisation som använder en gemensam Azure AD-klientorganisation.

Innan du beviljar Kör som-kontobehörigheter måste du först anteckna visningsnamnet för tjänstens huvudnamn som ska tilldelas.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Från ditt Automation-konto väljer du **Kör som-konton** under **Kontoinställningar.**
1. Välj **Kör som-konto i Azure.**
1. Kopiera eller anteckna värdet för **Visningsnamn på** sidan **Kör som-konto i Azure.**

Detaljerade anvisningar för hur du lägger till rolltilldelningar finns i följande artiklar beroende på vilken metod du vill använda.

* [Tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Tilldela Azure-roller med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Tilldela Azure-roller med hjälp av Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Tilldela Azure-roller med hjälp av REST API](..//role-based-access-control/role-assignments-rest.md)

När du har tilldelar rollen Kör som-kontot anger du i din runbook att `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` prenumerationskontexten ska användas. Mer information finns i [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Begränsa Kör som-kontobehörigheter

Om du vill styra automatiseringens mål mot resurser i Azure kan du köra [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) skript. Det här skriptet ändrar ditt befintliga tjänsthuvudnamn för Kör som-kontot för att skapa och använda en anpassad rolldefinition. Rollen har behörighet för alla resurser utom [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>När du har **körtUpdate-AutomationRunAsAccountRoleAssignments.ps1-skriptet** fungerar inte längre runbooks som Key Vault med hjälp av Kör som-konton. Innan du kör skriptet bör du granska runbooks i ditt konto för anrop till Azure Key Vault. Om du vill ge åtkomst Key Vault från Azure Automation runbooks måste du lägga till [Kör som-kontot i Key Vault-behörigheterna](#add-permissions-to-key-vault).

Om du behöver begränsa vad Kör som-tjänstens huvudnamn kan göra ytterligare kan du lägga till andra resurstyper i elementet i den `NotActions` anpassade rolldefinitionen. I följande exempel begränsas åtkomsten till `Microsoft.Compute/*` . Om du lägger till den här `NotActions` resurstypen till för rolldefinitionen kan rollen inte komma åt någon Beräkningsresurs. Mer information om rolldefinitioner finns i [Förstå rolldefinitioner för Azure-resurser.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Du kan avgöra om tjänstens huvudnamn som används av ditt Kör som-konto har **tilldelats rollen** Deltagare eller ett anpassat.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till ditt Automation-konto och **välj Kör som-konton** i avsnittet kontoinställningar.
1. Välj **Kör som-konto i Azure.**
1. Välj **Roll** för att hitta rolldefinitionen som används.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Verifiera rollen Kör som-konto." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Du kan också fastställa rolldefinitionen som används av Kör som-konton för flera prenumerationer eller Automation-konton. Gör detta med hjälp [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) skriptet i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägga till behörigheter i Key Vault

Du kan tillåta Azure Automation att kontrollera Key Vault och tjänstens huvudnamn för Kör som-kontot använder en anpassad rolldefinition. Du måste:

* Bevilja behörigheter till Key Vault.
* Ange åtkomstprincipen.

Du kan använda [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) i PowerShell-galleriet för att ge ditt Kör som-konto behörighet att Key Vault. Se [Tilldela en Key Vault åtkomstprincip för](../key-vault/general/assign-access-policy-powershell.md) mer information om hur du anger behörigheter för Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Lösa problem med konfigurationsfel för Kör som-konton

Vissa konfigurationsobjekt som krävs för ett Kör som-konto eller ett klassiskt Kör som-konto kan ha tagits bort eller skapats felaktigt under den första installationen. Möjliga instanser av felaktig konfiguration är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto som tagits bort från deltagarrollen
* Huvudnamn för tjänsten eller program i Azure AD

För sådana felkonfigurationsinstanser identifierar Automation-kontot ändringarna och visar  statusen Ofullständig i egenskapsfönstret För Kör som-konton för kontot.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Ofullständig Kör som-kontokonfiguration.":::

När du väljer Kör som-kontot visas följande felmeddelande i fönstret För kontoegenskaper:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med Kör som-kontot [genom att](delete-run-as-account.md) ta bort och återskapa Kör [som-kontot.](create-run-as-account.md)

## <a name="next-steps"></a>Nästa steg

* [Programobjekt och tjänstens huvudnamnsobjekt](../active-directory/develop/app-objects-and-service-principals.md).
* [Certifikatöversikt för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* Information om hur du skapar eller skapar ett Kör som-konto finns i [Skapa ett Kör som-konto.](create-run-as-account.md)
* Om du inte längre behöver använda ett Kör som-konto kan du se Ta [bort ett Kör som-konto.](delete-run-as-account.md)
