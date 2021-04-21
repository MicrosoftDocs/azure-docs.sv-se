---
title: Felsöka Azure RBAC
description: Felsöka problem med rollbaserad åtkomstkontroll i Azure (Azure RBAC).
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/06/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d816854c8d8a78931060c6e56fffbaee1fde5150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771721"
---
# <a name="troubleshoot-azure-rbac"></a>Felsöka Azure RBAC

I den här artikeln får du svar på några vanliga frågor om rollbaserad åtkomstkontroll i Azure (Azure RBAC), så att du vet vad du kan förvänta dig när du använder rollerna och kan felsöka åtkomstproblem.

## <a name="azure-role-assignments-limit"></a>Gräns för Azure-rolltilldelningar

Azure har stöd för upp till **2000** rolltilldelningar per prenumeration. Den här gränsen omfattar rolltilldelningar för prenumerationen, resursgruppen och resursomfången. Om du får felmeddelandet "Inga fler rolltilldelningar kan skapas (kod: RoleAssignmentLimitExceeded)" när du försöker tilldela en roll kan du försöka minska antalet rolltilldelningar i prenumerationen.

> [!NOTE]
> Gränsen **på 2 000** rolltilldelningar per prenumeration är fast och kan inte ökas.

Om du närmar dig den här gränsen kan du minska antalet rolltilldelningar på följande sätt:

- Lägg till användare i grupper och tilldela roller till grupperna i stället. 
- Kombinera flera inbyggda roller med en anpassad roll. 
- Skapa vanliga rolltilldelningar i ett högre omfång, till exempel prenumeration eller hanteringsgrupp.
- Om du har Azure AD Premium P2 gör du rolltilldelningar berättigade [i Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) stället för permanent tilldelade. 
- Lägg till ytterligare en prenumeration. 

Om du vill hämta antalet rolltilldelningar kan du visa diagrammet på sidan [Åtkomstkontroll (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) i Azure Portal. Du kan också använda följande Azure PowerShell kommandon:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problem med Azure-rolltilldelningar

- Om du inte kan tilldela en roll i Azure Portal på åtkomstkontroll  **(IAM)** eftersom alternativet Lägg till lägg till rolltilldelning är inaktiverat eller eftersom du får behörighetsfelet "Klienten med objekt-ID har inte behörighet att utföra åtgärden" kontrollerar du att du är inloggad med en användare som har tilldelats en roll som har behörighet som Ägare eller Administratör för användaråtkomst i det omfång som du försöker tilldela  >   `Microsoft.Authorization/roleAssignments/write` rollen. [](built-in-roles.md#owner) [](built-in-roles.md#user-access-administrator)
- Om du använder ett huvudnamn för tjänsten för att tilldela roller kan du få felet "Otillräcklig behörighet för att slutföra åtgärden". Anta till exempel att du har ett huvudnamn för tjänsten som har tilldelats rollen Ägare och du försöker skapa följande rolltilldelning som tjänstens huvudnamn med hjälp av Azure CLI:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Om du får felet "Otillräcklig behörighet för att slutföra åtgärden" beror det förmodligen på att Azure CLI försöker leta upp identiteten för tilldelningsmottagare i Azure AD och tjänstens huvudnamn inte kan läsa Azure AD som standard.

    Det finns två sätt att eventuellt lösa det här felet. Det första sättet är att tilldela rollen [Katalogläsare](../active-directory/roles/permissions-reference.md#directory-readers) till tjänstens huvudnamn så att det kan läsa data i katalogen.

    Det andra sättet att lösa det här felet är att skapa rolltilldelningen med hjälp av `--assignee-object-id` parametern i stället för `--assignee` . Med hjälp `--assignee-object-id` av hoppar Azure CLI över Azure AD-sökning. Du måste hämta objekt-ID:t för den användare, grupp eller det program som du vill tilldela rollen till. Mer information finns i Tilldela [Azure-roller med hjälp av Azure CLI.](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- Om du skapar ett nytt huvudnamn för tjänsten och omedelbart försöker tilldela en roll till tjänstens huvudnamn kan rolltilldelningen misslyckas i vissa fall.

    För att hantera det här scenariot bör du ange `principalType` egenskapen till när du skapar `ServicePrincipal` rolltilldelningen. Du måste också ange `apiVersion` för rolltilldelningen till `2018-09-01-preview` eller senare. Mer information finns i Tilldela Azure-roller till ett nytt huvudnamn för tjänsten med [hjälp av REST API](role-assignments-rest.md#new-service-principal) eller Tilldela Azure-roller till ett nytt huvudnamn för tjänsten [med hjälp Azure Resource Manager mallar](role-assignments-template.md#new-service-principal)

- Om du försöker ta bort den senaste ägarrolltilldelningen för en prenumeration kan du se felet "Det går inte att ta bort den senaste RBAC-administratörstilldelningen". Det går inte att ta bort den senaste ägarrolltilldelningen för en prenumeration för att undvika att prenumerationen blir överbliven. Om du vill avbryta din prenumeration kan du läsa [Avbryta din Azure-prenumeration.](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="problems-with-custom-roles"></a>Problem med anpassade roller

- Om du behöver anvisningar för hur du skapar en anpassad roll kan du gå till självstudierna om anpassade roller [med hjälp av Azure Portal,](custom-roles-portal.md) [Azure PowerShell](tutorial-custom-role-powershell.md)eller [Azure CLI](tutorial-custom-role-cli.md).
- Om du inte kan uppdatera en befintlig anpassad roll kontrollerar du att du är inloggad med en användare som har tilldelats en roll som har behörighet som Ägare eller Administratör för `Microsoft.Authorization/roleDefinition/write` [användaråtkomst.](built-in-roles.md#user-access-administrator) [](built-in-roles.md#owner)
- Om du inte lyckas ta bort en anpassad roll och får felmeddelandet om att det finns befintliga rolltilldelningar som refererar till rollen (kod: RoleDefinitionHasAssignments), så finns det rolltilldelningar som fortfarande använder den anpassade rollen. Ta bort dessa rolltilldelningar och försök att ta bort den anpassade rollen igen.
- Om du får felmeddelandet ”Det högsta tillåtna antalet rolldefinitioner har överskridits. Inga fler rolldefinitioner kan skapas (kod: RoleDefinitionLimitExceeded)" när du försöker skapa en ny anpassad roll tar du bort eventuella anpassade roller som inte används. Azure har stöd för upp **till 5 000** anpassade roller i en katalog. (För Azure Tyskland Azure China 21Vianet är gränsen 2 000 anpassade roller.)
- Om du får ett felmeddelande som liknar "Klienten har behörighet att utföra åtgärden Microsoft.Authorization/roleDefinitions/write" för omfånget "/subscriptions/{subscriptionid}", men den länkade prenumerationen hittades inte" när du försöker uppdatera en anpassad roll kontrollerar du om ett eller flera [tilldelningsbara omfång](role-definitions.md#assignablescopes) har tagits bort i katalogen. Om omfånget har tagits bort ska du skapa en supportbegäran eftersom det inte finns någon självbetjäningslösning tillgänglig just nu.

## <a name="custom-roles-and-management-groups"></a>Anpassade roller och hanteringsgrupper

- Du kan bara definiera en hanteringsgrupp i `AssignableScopes` en anpassad roll. Att lägga till en hanteringsgrupp `AssignableScopes` i är för närvarande i förhandsversion.
- Anpassade roller med `DataActions` kan inte tilldelas i hanteringsgruppomfånget.
- Azure Resource Manager verifierar inte att hanteringsgruppen finns i rolldefinitionens tilldelningsbara omfång.
- Mer information om anpassade roller och hanteringsgrupper finns i [Organisera dina resurser med Azure-hanteringsgrupper.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Överföra en prenumeration till en annan katalog

- Om du behöver anvisningar för hur du överför en prenumeration till en annan Azure AD-katalog kan du läsa Överföra en [Azure-prenumeration till en annan Azure AD-katalog.](transfer-subscription.md)
- Om du överför en prenumeration till en annan Azure AD-katalog tas alla rolltilldelningar **bort permanent** från Azure AD-källkatalogen och migreras inte till Azure AD-målkatalogen. Du måste skapa rolltilldelningarna på nytt i målkatalogen. Du måste också återskapa hanterade identiteter manuellt för Azure-resurser. Mer information finns i [Vanliga frågor och svar och kända problem med hanterade identiteter.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Om du är global administratör för Azure AD och du inte har åtkomst till en prenumeration efter att den [](elevate-access-global-admin.md) har överförts mellan kataloger använder du växlingsknappen Åtkomsthantering för **Azure-resurser** för att tillfälligt höja din åtkomst för att få åtkomst till prenumerationen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problem med tjänstadministratörer eller medadministratörer

- Om du har problem med Tjänstadministratör eller medadministratörer kan du läsa Lägga till eller ändra azure-prenumerationsadministratörer och administratörsroller för klassiska prenumerationer, Azure-roller och [Azure AD-roller.](rbac-and-directory-admin-roles.md) [](../cost-management-billing/manage/add-change-subscription-administrator.md)

## <a name="access-denied-or-permission-errors"></a>Åtkomst nekad eller behörighetsfel

- Om du får behörighetsfelet ”Klienten med objekt-ID har inte behörighet att utföra åtgärden över område (kod: AuthorizationFailed)” när du försöker skapa en resurs kan du kontrollera att du är inloggad med en användare med en roll som har skrivbehörighet till resursen i det valda omfånget. Om du vill hantera virtuella datorer i en resursgrupp ska du till exempel ha rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) på den resursgruppen (eller ett överordnat område). En lista över behörigheterna för varje inbyggd roll finns i [Inbyggda Roller i Azure.](built-in-roles.md)
- Om du får behörighetsfelet "Du har inte behörighet att skapa en supportbegäran" när du försöker skapa eller uppdatera en supportbegäran kontrollerar du att du är inloggad med en användare som har tilldelats en roll som har behörigheten, till exempel `Microsoft.Support/supportTickets/write` [Supportbegärandedeltagare.](built-in-roles.md#support-request-contributor)

## <a name="move-resources-with-role-assignments"></a>Flytta resurser med rolltilldelningar

Om du flyttar en resurs som har en Azure-roll som tilldelats direkt till resursen (eller en underordnad resurs), flyttas inte rolltilldelningen och blir överbliven. Efter flytten måste du skapa rolltilldelningen på nytt. Slutligen tas den överblivna rolltilldelningen bort automatiskt, men det är bästa praxis att ta bort rolltilldelningen innan du flyttar resursen.

Information om hur du flyttar resurser finns i [Flytta resurser till en ny resursgrupp eller prenumeration.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="role-assignments-with-identity-not-found"></a>Rolltilldelningar med identitet hittades inte

I listan över rolltilldelningar för Azure Portal kanske du märker att säkerhetsobjekt (användare, grupp, tjänstens huvudnamn eller hanterad identitet) visas som Identitet hittades inte med en **okänd** typ. 

![Identiteten hittades inte i Azure-rolltilldelningar](./media/troubleshooting/unknown-security-principal.png)

Identiteten kanske inte hittas av två orsaker:

- Du har nyligen bjudit in en användare när du skapar en rolltilldelning
- Du har tagit bort ett säkerhetsobjekt som hade en rolltilldelning

Om du nyligen bjudit in en användare när du skapar en rolltilldelning kan det här säkerhetsobjekt fortfarande vara i replikeringsprocessen mellan regioner. I så fall väntar du en stund och uppdaterar listan över rolltilldelningar.

Men om säkerhetsobjekt inte är en nyligen inbjuden användare kan det vara ett borttagna säkerhetsobjekt. Om du tilldelar en roll till ett säkerhetsobjekt och sedan tar bort säkerhetsobjekt utan att först ta bort rolltilldelningen, visas säkerhetsobjekt som **Identitet** hittades inte och **en Okänd** typ.

Om du visar den här rolltilldelningen med Azure PowerShell kan du se en tom `DisplayName` och en inställd på `ObjectType` **Okänd**. Till exempel [returnerar Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) en rolltilldelning som liknar följande utdata:

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

Om du listar den här rolltilldelningen med Hjälp av Azure CLI kan du också se en tom `principalName` . Till exempel [returnerar az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) en rolltilldelning som liknar följande utdata:

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

Det är inte något problem att lämna dessa rolltilldelningar där säkerhetsobjekt har tagits bort. Om du vill kan du ta bort dessa rolltilldelningar med hjälp av steg som liknar andra rolltilldelningar. Information om hur du tar bort rolltilldelningar finns i [Ta bort Azure-rolltilldelningar.](role-assignments-remove.md)

Om du i PowerShell försöker ta bort rolltilldelningarna med hjälp av objekt-ID:t och rolldefinitionsnamnet, och mer än en rolltilldelning matchar dina parametrar, får du felmeddelandet: "Den angivna informationen mappar inte till en rolltilldelning". Följande utdata visar ett exempel på felmeddelandet:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Om du får det här felmeddelandet kontrollerar du att du även anger `-Scope` `-ResourceGroupName` parametrarna eller .

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Rolltilldelningsändringar identifieras inte

Azure Resource Manager cachelagrar ibland konfigurationer och data för att förbättra prestandan. När du tilldelar roller eller tar bort rolltilldelningar kan det ta upp till 30 minuter innan ändringarna verkställs. Om du använder Azure Portal, Azure PowerShell eller Azure CLI kan du tvinga fram en uppdatering av dina rolltilldelningsändringar genom att logga ut och logga in. Om du gör ändringar i rolltilldelningen med REST API anrop kan du framt tvinga fram en uppdatering genom att uppdatera din åtkomsttoken.

Om du lägger till eller tar bort en rolltilldelning i hanteringsgruppomfånget och rollen har , kanske åtkomsten på dataplanet inte uppdateras `DataActions` på flera timmar. Detta gäller endast för hanteringsgruppsomfånget och dataplanet.

## <a name="web-app-features-that-require-write-access"></a>Webbappsfunktioner som kräver skrivåtkomst

Om du ger en användare skrivskyddad åtkomst till en enda webbapp inaktiveras vissa funktioner som du kanske inte förväntar dig. Följande hanteringsfunktioner kräver **skrivåtkomst** till en webbapp (antingen Deltagare eller Ägare) och är inte tillgängliga i något skrivskyddade scenario.

* Kommandon (t.ex. start, stopp osv.)
* Ändra inställningar som allmän konfiguration, skalningsinställningar, säkerhetskopieringsinställningar och övervakningsinställningar
* Åtkomst till autentiseringsuppgifter för publicering och andra hemligheter som appinställningar och anslutningssträngar
* Direktuppspelningsloggar
* Konfiguration av resursloggar
* Konsol (kommandotolk)
* Aktiva och senaste distributioner (för kontinuerlig lokal git-distribution)
* Uppskattade utgifter
* Webbtester
* Virtuellt nätverk (endast synligt för en läsare om ett virtuellt nätverk tidigare har konfigurerats av en användare med skrivbehörighet).

Om du inte kan komma åt någon av dessa paneler måste du be administratören om deltagaråtkomst till webbappen.

## <a name="web-app-resources-that-require-write-access"></a>Webbappresurser som kräver skrivåtkomst

Webbappar är komplicerade på grund av att det finns några olika resurser som samspelar. Här är en typisk resursgrupp med ett par webbplatser:

![Resursgrupp för webbapp](./media/troubleshooting/website-resource-model.png)

Om du ger någon åtkomst till bara webbappen inaktiveras därför en stor del av funktionerna på webbplatsbladet Azure Portal webbappen.

De här **objekten** kräver **skrivåtkomst till App Service plan** som motsvarar din webbplats:  

* Visa webbappens prisnivå (kostnadsfri eller Standard)  
* Skalningskonfiguration (antal instanser, storlek på virtuell dator, inställningar för automatisk skalning)  
* Kvoter (lagring, bandbredd, CPU)  

De här **objekten kräver** skrivåtkomst till **hela resursgruppen** som innehåller din webbplats:  

* TLS/SSL-certifikat och bindningar (TLS/SSL-certifikat kan delas mellan platser i samma resursgrupp och geo-plats)  
* Aviseringsregler  
* Inställningar för automatisk skalning  
* Application Insights-komponenter  
* Webbtester  

## <a name="virtual-machine-features-that-require-write-access"></a>Funktioner för virtuella datorer som kräver skrivåtkomst

Precis som med webbappar kräver vissa funktioner på bladet för den virtuella datorn skrivåtkomst till den virtuella datorn eller till andra resurser i resursgruppen.

Virtuella datorer är relaterade till domännamn, virtuella nätverk, lagringskonton och aviseringsregler.

De här **objekten kräver** skrivåtkomst till **den virtuella datorn**:

* Slutpunkter  
* IP-adresser  
* Diskar  
* Tillägg  

Dessa kräver **skrivåtkomst** till både **den virtuella** datorn och **resursgruppen** (tillsammans med domännamnet) som den finns i:  

* Tillgänglighetsuppsättning  
* Belastningsutjämnad uppsättning  
* Aviseringsregler  

Om du inte kan komma åt någon av dessa paneler ber du administratören om deltagaråtkomst till resursgruppen.

## <a name="azure-functions-and-write-access"></a>Azure Functions och skrivåtkomst

Vissa funktioner i [Azure Functions](../azure-functions/functions-overview.md) kräver skrivåtkomst. Om en användare till exempel har tilldelats [rollen](built-in-roles.md#reader) Läsare kan de inte visa funktionerna i en funktionsapp. Portalen visas **(ingen åtkomst).**

![Funktionsappar ingen åtkomst](./media/troubleshooting/functionapps-noaccess.png)

En läsare kan  klicka på fliken Plattformsfunktioner och sedan klicka på **Alla** inställningar för att visa några inställningar som är relaterade till en funktionsapp (liknar en webbapp), men de kan inte ändra någon av dessa inställningar. För att få åtkomst till dessa funktioner behöver du [rollen](built-in-roles.md#contributor) Deltagare.

## <a name="next-steps"></a>Nästa steg

- [Felsöka för gästanvändare](role-assignments-external-users.md#troubleshoot)
- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
- [Visa aktivitetsloggar för Azure RBAC-ändringar](change-history-report.md)
