---
title: Felsöka Azure RBAC
description: Felsök problem med rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d77468619fcd67887273b2fbd452b37add1e19b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555892"
---
# <a name="troubleshoot-azure-rbac"></a>Felsöka Azure RBAC

Den här artikeln ger svar på några vanliga frågor om rollbaserad åtkomst kontroll i Azure (Azure RBAC), så att du vet vad som ska förväntas när du använder rollerna och kan felsöka åtkomst problem.

## <a name="azure-role-assignments-limit"></a>Gräns för Azure Role-tilldelningar

Azure har stöd för upp till **2000** rolltilldelningar per prenumeration. Den här gränsen omfattar roll tilldelningar i prenumerations-, resurs grupps-och resurs omfång. Om du får fel meddelandet "det går inte att skapa fler roll tilldelningar (kod: RoleAssignmentLimitExceeded)" när du försöker tilldela en roll kan du försöka minska antalet roll tilldelningar i prenumerationen.

> [!NOTE]
> Gränsen för **2000** roll tilldelningar per prenumeration är fast och går inte att öka.

Om du får nära den här gränsen kan du minska antalet roll tilldelningar på följande sätt:

- Lägg till användare i grupper och tilldela roller till grupperna i stället. 
- Kombinera flera inbyggda roller med en anpassad roll. 
- Gör vanliga roll tilldelningar till en högre omfattning, till exempel prenumeration eller hanterings grupp.
- Om du har Azure AD Premium P2 gör du roll tilldelningar berättigade i [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) i stället för att permanent tilldelas. 
- Lägg till ytterligare en prenumeration. 

För att få antalet roll tilldelningar kan du Visa [diagrammet på sidan åtkomst kontroll (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) i Azure Portal. Du kan också använda följande Azure PowerShell-kommandon:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problem med Azure Role-tilldelningar

- Om du inte kan tilldela en roll i Azure Portal på **åtkomst kontroll (IAM)** eftersom alternativet Lägg till   >  **roll tilldelning för tilldelning** är inaktiverat eller om du får behörighets felet "klienten med objekt-ID har inte behörighet att utföra åtgärden", kontrol lera att du är inloggad med en användare som har `Microsoft.Authorization/roleAssignments/write` behörighet som [ägare](built-in-roles.md#owner) eller [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) i den omfattning som du försöker tilldela rollen.
- Om du använder ett huvud namn för tjänsten för att tilldela roller kan du få fel meddelandet "otillräcklig behörighet för att slutföra åtgärden". Anta till exempel att du har ett huvud namn för tjänsten som har tilldelats ägar rollen och du försöker skapa följande roll tilldelning som tjänstens huvud namn med Azure CLI:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Om du får felet "otillräcklig behörighet för att slutföra åtgärden" är det troligt att Azure CLI försöker leta upp den tilldelade identiteten i Azure AD och tjänstens huvud namn kan inte läsa Azure AD som standard.

    Det finns två sätt att eventuellt lösa det här felet. Det första sättet är att tilldela [katalog läsar](../active-directory/roles/permissions-reference.md#directory-readers) rollen till tjänstens huvud namn, så att den kan läsa data i katalogen.

    Det andra sättet att lösa det här felet är att skapa roll tilldelningen med hjälp av- `--assignee-object-id` parametern i stället för `--assignee` . Genom `--assignee-object-id` att använda, hoppar Azure CLI över Azure AD-sökningen. Du måste hämta objekt-ID: t för den användare, grupp eller det program som du vill tilldela rollen till. Mer information finns i [tilldela Azure-roller med Azure CLI](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Om du försöker ta bort den senaste ägar tilldelningen för en prenumeration kan du se felet "det går inte att ta bort den sista rollen för RBAC-admin". Det går inte att ta bort den senaste ägarens roll tilldelning för en prenumeration för att undvika överblivna prenumerationen. Om du vill avbryta din prenumeration kan du läsa mer i [avbryta din Azure-prenumeration](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Problem med anpassade roller

- Om du behöver steg för att skapa en anpassad roll kan du läsa självstudierna för den anpassade rollen med hjälp av [Azure Portal](custom-roles-portal.md) (för närvarande i för hands version), [Azure POWERSHELL](tutorial-custom-role-powershell.md)eller [Azure CLI](tutorial-custom-role-cli.md).
- Om du inte kan uppdatera en befintlig anpassad roll kontrollerar du att du är inloggad med en användare som har tilldelats en roll med `Microsoft.Authorization/roleDefinition/write` behörighet som [ägare](built-in-roles.md#owner) eller administratör för [användar åtkomst](built-in-roles.md#user-access-administrator).
- Om du inte lyckas ta bort en anpassad roll och får felmeddelandet om att det finns befintliga rolltilldelningar som refererar till rollen (kod: RoleDefinitionHasAssignments), så finns det rolltilldelningar som fortfarande använder den anpassade rollen. Ta bort dessa rolltilldelningar och försök att ta bort den anpassade rollen igen.
- Om du får felmeddelandet ”Det högsta tillåtna antalet rolldefinitioner har överskridits. Inga fler roll definitioner kan skapas (kod: RoleDefinitionLimitExceeded) "när du försöker skapa en ny anpassad roll, tar du bort alla anpassade roller som inte används. Azure har stöd för upp till **5000** anpassade roller i en katalog. (För Azure Germany och Azure Kina är gränsen 2000 anpassade roller.)
- Om du får ett fel som liknar "klienten har behörighet att utföra åtgärden" Microsoft. Authorization/roleDefinitions/Write "i omfånget"/Subscriptions/{subscriptionId} ", gick det inte att hitta den länkade prenumerationen när du försöker uppdatera en anpassad roll, kontrol lera om ett eller flera [tilldelnings bara scope](role-definitions.md#assignablescopes) har tagits bort i katalogen. Om omfånget har tagits bort ska du skapa en supportbegäran eftersom det inte finns någon självbetjäningslösning tillgänglig just nu.

## <a name="custom-roles-and-management-groups"></a>Anpassade roller och hanterings grupper

- Du kan bara definiera en hanterings grupp i `AssignableScopes` för en anpassad roll. Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
- `DataActions`Det går inte att tilldela anpassade roller med hanterings grupps omfånget.
- Azure Resource Manager validerar inte hanterings gruppens existens i roll definitionens tilldelnings omfång.
- Mer information om anpassade roller och hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Överföra en prenumeration till en annan katalog

- Om du behöver steg för att överföra en prenumeration till en annan Azure AD-katalog, se [överföra en Azure-prenumeration till en annan Azure AD-katalog](transfer-subscription.md).
- Om du överför en prenumeration till en annan Azure AD-katalog tas alla roll tilldelningar bort **permanent** från källan till Azure AD-katalogen och migreras inte till Azure AD-katalogen. Du måste återskapa roll tilldelningarna i mål katalogen. Du måste också manuellt återskapa hanterade identiteter för Azure-resurser. Mer information finns i [vanliga frågor och svar och kända problem med hanterade identiteter](../active-directory/managed-identities-azure-resources/known-issues.md).
- Om du är global administratör i Azure AD och du inte har åtkomst till en prenumeration efter att den överförts mellan kataloger, använder du **åtkomst hantering för Azure-resurser** för att tillfälligt [öka åtkomsten](elevate-access-global-admin.md) till prenumerationen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problem med tjänstadministratörer eller medadministratörer

- Om du har problem med tjänst administratören eller medadministratörer kan du läsa mer i [lägga till eller ändra Azure-prenumerations administratörer](../cost-management-billing/manage/add-change-subscription-administrator.md) och [klassiska prenumerations administratörs roller, Azure-roller och Azure AD-roller](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Åtkomst nekad eller behörighets fel

- Om du får behörighetsfelet ”Klienten med objekt-ID har inte behörighet att utföra åtgärden över område (kod: AuthorizationFailed)” när du försöker skapa en resurs kan du kontrollera att du är inloggad med en användare med en roll som har skrivbehörighet till resursen i det valda omfånget. Om du vill hantera virtuella datorer i en resursgrupp ska du till exempel ha rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) på den resursgruppen (eller ett överordnat område). En lista över behörigheter för varje inbyggd roll finns i [inbyggda roller i Azure](built-in-roles.md).
- Om du får behörighets fel "du har inte behörighet att skapa en supportbegäran" när du försöker skapa eller uppdatera ett support ärende, kontrollerar du att du är inloggad med en användare som har `Microsoft.Support/supportTickets/write` behörighet, till exempel [deltagare i support förfrågan](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Flytta resurser med roll tilldelningar

Om du flyttar en resurs som har en Azure-roll som tilldelats direkt till resursen (eller en underordnad resurs) flyttas inte roll tilldelningen och blir överbliven. När du har flyttat måste du återskapa roll tilldelningen. Slutligen tas den överblivna roll tilldelningen bort automatiskt, men det är en bra idé att ta bort roll tilldelningen innan du flyttar resursen.

Information om hur du flyttar resurser finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Roll tilldelningar med identitet hittades inte

I listan över roll tilldelningar för Azure Portal kan du märka att säkerhetsobjektet (användare, grupp, tjänstens huvud namn eller hanterad identitet) anges som **identitet inte hittas** med en **okänd** typ.

![Identiteten hittades inte i Azure Role-tilldelningar](./media/troubleshooting/unknown-security-principal.png)

Identiteten kanske inte hittas av två skäl:

- Du har nyligen bjudit in en användare när du skapade en roll tilldelning
- Du har tagit bort ett säkerhets objekt som hade en roll tilldelning

Om du nyligen har bjudit in en användare när du skapade en roll tilldelning, kan det här säkerhets objekt fortfarande finnas i replikeringen över flera regioner. I så fall kan du vänta en stund och uppdatera listan roll tilldelningar.

Men om detta säkerhets objekt inte är en nyligen inbjuden användare kan det vara ett borttaget säkerhets objekt. Om du tilldelar en roll till ett säkerhets objekt och sedan tar bort säkerhetsobjektet utan att först ta bort roll tilldelningen visas säkerhets objekt listan som **identitet inte hittas** och en **okänd** typ.

Om du anger den här roll tilldelningen med Azure PowerShell kan du se att en tom `DisplayName` och en `ObjectType` uppsättning är **okänd**. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returnerar till exempel en roll tilldelning som liknar följande utdata:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

På samma sätt kan det hända att du ser en tom lista om du anger den här roll tilldelningen med hjälp av Azure CLI `principalName` . Till exempel returnerar [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list) en roll tilldelning som liknar följande utdata:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Det är inte ett problem att lämna roll tilldelningarna där säkerhets objekt har tagits bort. Om du vill kan du ta bort roll tilldelningarna med hjälp av steg som liknar andra roll tilldelningar. Information om hur du tar bort roll tilldelningar finns i [ta bort roll tilldelningar i Azure](role-assignments-remove.md).

Om du försöker ta bort roll tilldelningarna med objekt-ID: t och roll definitions namnet i PowerShell och fler än en roll tilldelning matchar dina parametrar visas följande fel meddelande: "den angivna informationen mappas inte till en roll tilldelning". Följande utdata visar ett exempel på fel meddelandet:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Om du får det här fel meddelandet ser du till att du även `-Scope` anger `-ResourceGroupName` parametrarna eller.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Roll tilldelnings ändringar identifieras inte

Azure Resource Manager cachelagrar ibland konfigurationer och data för att förbättra prestanda. När du tilldelar roller eller tar bort roll tilldelningar kan det ta upp till 30 minuter innan ändringarna börjar gälla. Om du använder Azure Portal, Azure PowerShell eller Azure CLI kan du framtvinga en uppdatering av roll tilldelnings ändringarna genom att logga ut och logga in. Om du gör roll tilldelnings ändringar med REST API-anrop kan du framtvinga en uppdatering genom att uppdatera åtkomst-token.

Om du lägger till eller tar bort en roll tilldelning i hanterings gruppens omfång och rollen har `DataActions` , kanske inte åtkomsten till data planet uppdateras under flera timmar. Detta gäller endast för hanterings gruppens omfattning och data planet.

## <a name="web-app-features-that-require-write-access"></a>Webb program funktioner som kräver skriv åtkomst

Om du beviljar en användare skrivskyddad åtkomst till en enda webbapp inaktive ras vissa funktioner som du kanske inte förväntar dig. Följande hanterings funktioner kräver **Skriv** åtkomst till en webbapp (antingen deltagare eller ägare) och är inte tillgängliga i alla skrivskyddade scenarier.

* Kommandon (t. ex. Start, stopp osv.)
* Ändra inställningar som allmän konfiguration, skalnings inställningar, säkerhets kopierings inställningar och övervaknings inställningar
* Åtkomst till autentiseringsuppgifter för publicering och andra hemligheter som appinställningar och anslutnings strängar
* Direktuppspelningsloggar
* Konfiguration av resurs loggar
* Konsol (kommando tolk)
* Aktiva och nya distributioner (för lokal git-kontinuerlig distribution)
* Beräknade utgifter
* Webbtester
* Virtuellt nätverk (endast synligt för en läsare om ett virtuellt nätverk tidigare har kon figurer ATS av en användare med Skriv behörighet).

Om du inte kan komma åt någon av dessa paneler måste du be administratören om deltagar åtkomst till webbappen.

## <a name="web-app-resources-that-require-write-access"></a>Webb program resurser som kräver skriv åtkomst

Webbappar är komplicerade genom förekomst av några olika resurser som spelas upp. Här är en typisk resurs grupp med ett par webbplatser:

![Resurs grupp för webb program](./media/troubleshooting/website-resource-model.png)

Det innebär att om du beviljar någon åtkomst till webbappen är många av funktionerna på bladet webbplats i Azure Portal inaktiverade.

De här objekten kräver **Skriv** åtkomst till den **App Service plan** som motsvarar din webbplats:  

* Visa Web Apps pris nivå (kostnads fri eller standard)  
* Skalnings konfiguration (antal instanser, storlek på virtuell dator, inställningar för automatisk skalning)  
* Kvoter (lagring, bandbredd, CPU)  

De här objekten kräver **Skriv** åtkomst till hela **resurs gruppen** som innehåller din webbplats:  

* TLS/SSL-certifikat och bindningar (TLS/SSL-certifikat kan delas mellan platser i samma resurs grupp och Geo-plats)  
* Aviseringsregler  
* Inställningar för autoskalning  
* Application Insights-komponenter  
* Webbtester  

## <a name="virtual-machine-features-that-require-write-access"></a>Funktioner för virtuella datorer som kräver skriv åtkomst

På liknande sätt som Web Apps kräver vissa funktioner på bladet virtuell dator skriv åtkomst till den virtuella datorn eller till andra resurser i resurs gruppen.

Virtuella datorer är relaterade till domän namn, virtuella nätverk, lagrings konton och varnings regler.

De här objekten kräver **Skriv** åtkomst till den **virtuella datorn**:

* Slutpunkter  
* IP-adresser  
* Diskar  
* Tillägg  

Dessa kräver **Skriv** behörighet till både den **virtuella datorn** och **resurs gruppen** (tillsammans med domän namnet) som den finns i:  

* Tillgänglighetsuppsättning  
* Belastningsutjämnad uppsättning  
* Aviseringsregler  

Om du inte kan komma åt någon av dessa paneler kan du be administratören om deltagar åtkomst till resurs gruppen.

## <a name="azure-functions-and-write-access"></a>Azure Functions-och skriv åtkomst

Vissa funktioner i [Azure Functions](../azure-functions/functions-overview.md) kräver skriv åtkomst. Om en användare till exempel har tilldelats rollen [läsare](built-in-roles.md#reader) kan de inte Visa funktionerna i en Function-app. Portalen visar **(ingen åtkomst)**.

![Function-appar ingen åtkomst](./media/troubleshooting/functionapps-noaccess.png)

En läsare kan klicka på fliken **plattforms funktioner** och sedan klicka på **alla inställningar** för att visa vissa inställningar som är relaterade till en Function-app (liknar en webbapp), men de kan inte ändra någon av dessa inställningar. Du behöver [deltagar](built-in-roles.md#contributor) rollen för att få åtkomst till dessa funktioner.

## <a name="next-steps"></a>Nästa steg

- [Felsöka för gäst användare](role-assignments-external-users.md#troubleshoot)
- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
- [Visa aktivitets loggar för Azure RBAC-ändringar](change-history-report.md)
