---
title: Hantera ett Kör som-konto för Azure Automation
description: Den här artikeln beskriver hur du hanterar ditt kör som-konto med PowerShell eller från Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f9e99318c526bb935d0e035fdbf59874249390da
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050947"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Hantera ett Kör som-konto för Azure Automation

Kör som-konton i Azure Automation tillhandahåller autentisering för att hantera resurser i den klassiska distributions modellen Azure Resource Manager eller Azure med hjälp av Automation-runbooks och andra automatiserings funktioner. Den här artikeln innehåller rikt linjer för hur du hanterar ett Kör som-konto eller ett klassiskt kör som-konto.

Mer information om Azure Automation autentisering av konton och rikt linjer som rör process automatiserings scenarier finns i [Översikt över Automation-kontots autentisering](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Förnya ett självsignerat certifikat

Det självsignerade certifikatet som du har skapat för kör som-kontot går ut ett år från datumet då det skapades. Innan ditt kör som-konto går ut måste du förnya certifikatet. Du kan förnya det när som helst innan det upphör att gälla.

När du förnyar det självsignerade certifikatet behålls det aktuella giltiga certifikatet för att säkerställa att alla Runbooks som köas eller aktivt körs, och som autentiserar med kör som-kontot, inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

>[!NOTE]
>Om du tror att kör som-kontot har komprometterats kan du ta bort och återskapa det självsignerade certifikatet.

>[!NOTE]
>Om du har konfigurerat ditt kör som-konto för att använda ett certifikat som utfärdats av din företags certifikat utfärdare och du använder alternativet för att förnya ett självsignerat certifikat, ersätts företags certifikatet av ett självsignerat certifikat.

Använd följande steg för att förnya det självsignerade certifikatet.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till ditt Automation-konto och välj **Kör som-konton** i avsnittet konto inställningar.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Fönstret Egenskaper för Automation-konto.":::

1. På sidan Egenskaper för **Kör som-konton** väljer du antingen **Kör som-konto** eller **klassiskt kör som-konto** beroende på vilket konto du behöver för att förnya certifikatet för.

1. På sidan **Egenskaper** för det valda kontot väljer du **Förnya certifikat**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Förnya certifikat för kör som-konto.":::

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Bevilja behörigheter för kör som-konto i andra prenumerationer

Azure Automation har stöd för att använda ett enda Automation-konto från en prenumeration och köra Runbooks mot Azure Resource Manager resurser över flera prenumerationer. Den här konfigurationen stöder inte den klassiska Azure-distributions modellen.

Du tilldelar huvud kontot kör som-konto rollen [deltagare](../role-based-access-control/built-in-roles.md#contributor) i den andra prenumerationen eller mer restriktiva behörigheter. Mer information finns i [rollbaserad åtkomst kontroll](automation-role-based-access-control.md) i Azure Automation. För att tilldela kör som-kontot till rollen i den andra prenumerationen måste det användar konto som utför den här uppgiften vara medlem i **ägar** rollen i den prenumerationen.

> [!NOTE]
> Den här konfigurationen stöder bara flera prenumerationer av en organisation med en vanlig Azure AD-klient.

Innan du beviljar behörigheter för kör som-kontot måste du först anteckna visnings namnet för tjänstens huvud namn som ska tilldelas.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Från ditt Automation-konto väljer du **Kör som-konton** under **konto inställningar**.
1. Välj **Kör som-konto i Azure**.
1. Kopiera eller anteckna värdet för **visnings namn** på sidan **Kör som-konto i Azure** .

Detaljerade anvisningar för hur du lägger till roll tilldelningar finns i följande artiklar, beroende på vilken metod du vill använda.

* [Lägg till Azure-roll tilldelning från Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Lägg till Azure-roll tilldelning med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Lägg till roll tilldelning i Azure med hjälp av Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Lägg till roll tilldelning i Azure med hjälp av REST API](..//role-based-access-control/role-assignments-rest.md)

När du har tilldelat kör som-kontot till rollen anger du i din Runbook `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` att prenumerations kontexten ska användas. Mer information finns i [set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Begränsa behörigheter för kör som-konto

Om du vill styra automatiseringen av automatisering mot resurser i Azure kan du köra [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) -skriptet. Det här skriptet ändrar ditt befintliga huvud namn för kör som-kontot för att skapa och använda en anpassad roll definition. Rollen har behörigheter för alla resurser utom [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>När du har kört **Update-AutomationRunAsAccountRoleAssignments.ps1** skriptet fungerar inte Runbooks som har åtkomst Key Vault genom användningen av kör som-konton. Innan du kör skriptet bör du granska Runbooks i ditt konto för anrop till Azure Key Vault. Om du vill ge åtkomst till Key Vault från Azure Automation runbooks måste du [lägga till kör som-kontot i Key Vault behörigheter](#add-permissions-to-key-vault).

Om du behöver ytterligare begränsa vad som kan utföras av tjänstens huvud namn kan du lägga till andra resurs typer till `NotActions` elementet i den anpassade roll definitionen. I följande exempel begränsas åtkomsten till `Microsoft.Compute/*` . Om du lägger till den här resurs typen i `NotActions` för roll definitionen kommer rollen inte att kunna komma åt någon beräknings resurs. Mer information om roll definitioner finns i [förstå roll definitioner för Azure-resurser](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Du kan kontrol lera om tjänstens huvud namn som används av ditt kör som-konto har tilldelats **deltagar** rollen eller en anpassad.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till ditt Automation-konto och välj **Kör som-konton** i avsnittet konto inställningar.
1. Välj **Kör som-konto i Azure**.
1. Välj **roll** för att hitta den roll definition som används.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Kontrol lera rollen kör som-konto." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Du kan också bestämma vilken roll definition som används av kör som-kontona för flera prenumerationer eller Automation-konton. Gör detta med hjälp av [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) -skriptet i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägg till behörigheter i Key Vault

Du kan låta Azure Automation verifiera om Key Vault och ditt kör som-konto-tjänstens huvud namn använder en anpassad roll definition. Du måste:

* Bevilja behörighet till Key Vault.
* Ange åtkomst principen.

Du kan använda [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) -skriptet i PowerShell-galleriet för att ge ditt kör som-konto behörighet att Key Vault. Mer information om hur du ställer in behörigheter på Key Vault finns i [tilldela en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Lös problem med fel konfiguration för kör som-konton

Vissa konfigurations objekt som krävs för ett Kör som-eller klassiskt kör som-konto kan ha tagits bort eller skapats felaktigt under den första installationen. Möjliga instanser av felaktig konfiguration är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagar rollen
* Huvudnamn för tjänsten eller program i Azure AD

För sådana felkonfigurations instanser identifierar Automation-kontot ändringarna och visar statusen *ofullständig* i fönstret Egenskaper för kör som-konton för kontot.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Ofullständig konfiguration av kör som-konto.":::

När du väljer Kör som-kontot visas följande fel meddelande i fönstret konto egenskaper:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med kör som-kontot genom att [ta bort](delete-run-as-account.md) och återskapa kör som [-](create-run-as-account.md) kontot.

## <a name="next-steps"></a>Nästa steg

* [Program objekt och tjänst huvud objekt](../active-directory/develop/app-objects-and-service-principals.md).
* [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* Information om hur du skapar eller återskapar ett Kör som-konto finns i [skapa ett Kör som-konto](create-run-as-account.md).
* Om du inte längre behöver använda ett Kör som-konto kan du läsa [ta bort ett Kör som-konto](delete-run-as-account.md).