---
title: Skydda privata DNS-zoner och poster – Azure DNS
description: I den här utbildnings vägen börjar du skydda privata DNS-zoner och post uppsättningar i Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 6e77f983f3600ae7c54d7d88f2ad1a006d7325fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614198"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Skydda privata DNS-zoner och-poster

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Privat DNS zoner och poster är kritiska resurser. Att ta bort en DNS-zon eller en enskild DNS-post kan resultera i avbrott i tjänsten. Det är viktigt att DNS-zoner och-poster skyddas mot obehöriga eller oavsiktliga ändringar.

I den här artikeln förklaras hur Azure DNS hjälper dig att skydda dina privata DNS-zoner och poster mot sådana ändringar.  Vi tillämpar två kraftfulla säkerhetsfunktioner som tillhandahålls av Azure Resource Manager: [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) och [resurs lås](../azure-resource-manager/management/lock-resources.md).

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering för Azure-användare, grupper och resurser. Med Azure RBAC kan du ge den åtkomst nivå som användarna behöver. Mer information om hur Azure RBAC hjälper dig att hantera åtkomst finns i [Vad är rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>Rollen Privat DNS Zone Contributor

Rollen Privat DNS Zone Contributor är en inbyggd roll för hantering av privata DNS-resurser. Med den här rollen som används för en användare eller grupp kan de hantera privata DNS-resurser.

Resurs gruppen *myPrivateDNS* innehåller fem zoner för Contoso Corporation. Om du beviljar behörigheten DNS-administratör Privat DNS zon deltagare till den resurs gruppen ger fullständig kontroll över dessa DNS-zoner. Det förhindrar att du beviljar onödiga behörigheter. DNS-administratören kan inte skapa eller stoppa virtuella datorer.

Det enklaste sättet att tilldela Azure RBAC-behörigheter är [via Azure Portal](../role-based-access-control/role-assignments-portal.md).  

Öppna **åtkomst kontroll (IAM)** för resurs gruppen, Välj **Lägg till** och välj sedan rollen **privat DNS Zone Contributor** . Välj de användare eller grupper som krävs för att bevilja behörigheter.

![Resurs grupps nivå Azure RBAC via Azure Portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Behörigheter kan också [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Motsvarande kommando är också [tillgängligt via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>Nivå för privat zon Azure RBAC

Azure RBAC-regler kan användas för en prenumeration, en resurs grupp eller en enskild resurs. Resursen kan vara en enskild DNS-zon eller en enskild post uppsättning.

Resurs gruppen *myPrivateDNS* innehåller till exempel zonen *Private.contoso.com* och en under zon *Customers.Private.contoso.com*. CNAME-poster skapas för varje kund konto. Det administratörs konto som används för att hantera CNAME-poster tilldelas behörigheter för att skapa poster i *Customers.Private.contoso.com* -zonen. Kontot kan bara hantera *Customers.Private.contoso.com* .

Azure RBAC-behörigheter på Zone-nivå kan beviljas via Azure Portal.  Öppna **åtkomst kontroll (IAM)** för zonen, Välj **Lägg till** och välj sedan rollen **privat DNS Zone Contributor** . Välj de användare eller grupper som krävs för att bevilja behörigheter.

![DNS-zon nivå Azure RBAC via Azure Portal](./media/dns-protect-private-zones-recordsets/rbac2.png)

Behörigheter kan också [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Motsvarande kommando är också [tillgängligt via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Post uppsättnings nivå Azure RBAC

Behörigheter tillämpas på post uppsättnings nivån.  Användaren beviljas kontroll över de poster de behöver och kan inte göra några andra ändringar.

Det går att konfigurera Azure RBAC-behörigheter för Record-uppsättning via Azure Portal med hjälp av **Access Controls knappen (IAM)** på sidan post uppsättning:

![Skärm bild som visar knappen Access Control (I A M).](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Skärm bild som visar Access Control med Lägg till roll tilldelning markerad.](./media/dns-protect-private-zones-recordsets/rbac4.png)

Det går även att bevilja Azure RBAC-behörighet för Record-uppsättning [med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Motsvarande kommando är också [tillgängligt via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Anpassade roller

Den inbyggda rollen Privat DNS Zone Contributor ger fullständig kontroll över en DNS-resurs. Det är möjligt att skapa dina egna anpassade Azure-roller för att ge en bättre och mer detaljerad kontroll.

Kontot som används för att hantera CNAME beviljas behörighet att endast hantera CNAME-poster. Kontot kan inte ändra poster av andra typer. Kontot kan inte utföra åtgärder på zon nivå, till exempel zon borttagning.

I följande exempel visas en anpassad roll definition för hantering av CNAME-poster:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Egenskapen åtgärder definierar följande DNS-/regionsspecifika behörigheter:

* `Microsoft.Network/privateDnsZones/CNAME/*` ger fullständig kontroll över CNAME-poster
* `Microsoft.Network/privateDNSZones/read` ger behörighet att läsa DNS-privata zoner, men inte att ändra dem, så att du kan se i vilken zon som CNAME skapas.

> [!NOTE]
> Att använda en anpassad Azure-roll för att förhindra borttagning av post uppsättningar samtidigt som de fortfarande kan uppdateras är inte en effektiv kontroll. Det förhindrar att post uppsättningar tas bort, men den förhindrar inte att de ändras.  Tillåtna ändringar omfattar att lägga till och ta bort poster från post uppsättningen, inklusive att ta bort alla poster för att lämna en tom post uppsättning. Detta har samma resultat som när du tar bort post uppsättningen från en DNS-matchning.

Det går för närvarande inte att definiera anpassade roll definitioner via Azure Portal. En anpassad roll som baseras på den här roll definitionen kan skapas med hjälp av Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Den kan också skapas via Azure CLI:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Rollen kan sedan tilldelas på samma sätt som inbyggda roller, enligt beskrivningen ovan i den här artikeln.

Mer information om hur du skapar, hanterar och tilldelar anpassade roller finns i Azure- [anpassade roller](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Resurslås

Azure Resource Manager stöder en annan typ av säkerhets kontroll, möjligheten att låsa resurser. Resurs låsningar tillämpas på resursen och gäller för alla användare och roller. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Det finns två typer av resurs Lås: **CanNotDelete** och **ReadOnly**. Dessa lås typer kan användas antingen i en Privat DNS zon eller till en enskild post uppsättning.  I följande avsnitt beskrivs flera vanliga scenarier och hur du kan hantera dem med hjälp av resurs lås.

### <a name="protecting-against-all-changes"></a>Skydda mot alla ändringar

Använd ett skrivskyddat lås för zonen för att förhindra att ändringar görs. Det här låset förhindrar att nya post uppsättningar skapas och att befintliga post uppsättningar ändras eller tas bort.

Resurs lås för zonens nivå kan skapas via Azure Portal.  Välj **Lås** på sidan DNS-zon och välj sedan **+ Lägg till**:

![Resurs lås på zon nivå via Azure Portal](./media/dns-protect-private-zones-recordsets/locks1.png)

Resurs lås på zon-nivå kan också skapas via [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Motsvarande kommando är också [tillgängligt via Azure CLI](/cli/azure/lock#az-lock-create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Skydda enskilda poster

Om du vill förhindra att en befintlig DNS-post har ändrats använder du ett skrivskyddat lås till post uppsättningen.

> [!NOTE]
> Att använda ett CanNotDelete-lås till en post uppsättning är inte en effektiv kontroll. Det förhindrar att post uppsättningen tas bort, men den förhindrar inte att den ändras.  Tillåtna ändringar omfattar att lägga till och ta bort poster från post uppsättningen, inklusive att ta bort alla poster för att lämna en tom post uppsättning. Detta har samma resultat som när du tar bort post uppsättningen från en DNS-matchning.

Resurs lås för post uppsättnings nivå kan för närvarande bara konfigureras med hjälp av Azure PowerShell.  De stöds inte i Azure Portal eller Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Skydda mot zon borttagning

När en zon tas bort i Azure DNS raderas alla post uppsättningar i zonen.  Det går inte att återställa den här åtgärden. Att oavsiktligt ta bort en kritisk zon har möjlighet att få betydande påverkan på verksamheten.  Det är viktigt att skydda mot oavsiktlig zon borttagning.

Om du använder ett CanNotDelete-lås till en zon förhindrar du att zonen tas bort. Lås ärvs av underordnade resurser. Ett lås förhindrar att alla post uppsättningar i zonen tas bort. Som beskrivs i anteckningen ovan är det ineffektivt eftersom poster fortfarande kan tas bort från de befintliga post uppsättningarna.

Alternativt kan du använda ett CanNotDelete-lås till en post uppsättning i zonen, till exempel SOA-postuppsättningen. Zonen tas inte bort utan att även ta bort post uppsättningarna. Det här låset skyddar mot zon borttagning och tillåter fortfarande att post uppsättningar i zonen ändras fritt. Om det görs ett försök att ta bort zonen identifierar Azure Resource Manager borttagningen. Borttagningen tar också bort SOA-postuppsättningen, Azure Resource Manager blockerar anropet eftersom SOA är låst.  Inga post uppsättningar har tagits bort.

Följande PowerShell-kommando skapar ett CanNotDelete-lås mot SOA-posten för den aktuella zonen:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Ett annat alternativ för att förhindra borttagning av oavsiktliga zoner är att använda en anpassad roll. Den här rollen säkerställer att konton som används för att hantera dina zoner inte har behörighet att ta bort zonen. 

När du behöver ta bort en zon kan du framtvinga en borttagning av två steg:

 - Först, bevilja zon borttagnings behörigheter
 - Därefter beviljar du behörighet att ta bort zonen.

Den anpassade rollen fungerar för alla zoner som används av dessa konton. Konton med behörighet att ta bort zoner, till exempel Prenumerationens ägare, kan fortfarande ta bort en zon av misstag.

Det går att använda båda metoderna-resurs lås och anpassade roller – samtidigt, som en skydds djup metod för DNS-zonskydd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med Azure RBAC finns i [Vad är rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).
* Mer information om hur du arbetar med resurs lås finns i [låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
