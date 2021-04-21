---
title: Skydda DNS-zoner och -poster – Azure DNS
description: I den här utbildningsvägen kommer du igång med att skydda DNS-zoner och postuppsättningar i Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 9d65e024e9efa3ad2bcb1c70d44360c8bd0de384
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785884"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Så här skyddar du DNS-zoner och -poster

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS-zoner och -poster är kritiska resurser. Om du tar bort en DNS-zon eller en enda DNS-post kan det leda till avbrott i tjänsten. Det är viktigt att DNS-zoner och -poster skyddas mot obehöriga eller oavsiktliga ändringar.

Den här artikeln beskriver Azure DNS du kan skydda dina privata DNS-zoner och -poster mot sådana ändringar.  Vi använder två kraftfulla funktioner som tillhandahålls av Azure Resource Manager: rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../role-based-access-control/overview.md) och [resurslås.](../azure-resource-manager/management/lock-resources.md)

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Rollbaserad åtkomstkontroll i Azure (Azure RBAC) möjliggör mer information om åtkomsthantering för Azure-användare, grupper och resurser. Med Azure RBAC kan du bevilja den åtkomstnivå som användarna behöver. Mer information om hur Azure RBAC hjälper dig att hantera åtkomst finns i Vad är [rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/overview.md)

### <a name="the-dns-zone-contributor-role"></a>Rollen DNS-zondeltagare

Rollen DNS-zondeltagare är en inbyggd roll för att hantera privata DNS-resurser. Den här rollen som tillämpas på en användare eller grupp gör att de kan hantera DNS-resurser.

Resursgruppen *myResourceGroup innehåller* fem zoner för Contoso Corporation. Om du beviljar DNS-administratörens DNS-zondeltagare behörighet till den resursgruppen får du fullständig kontroll över dessa DNS-zoner. Det undviker att bevilja onödiga behörigheter. DNS-administratören kan inte skapa eller stoppa virtuella datorer.

Det enklaste sättet att tilldela Azure RBAC-behörigheter [är via Azure Portal](../role-based-access-control/role-assignments-portal.md).  

Öppna **Åtkomstkontroll (IAM)** för resursgruppen och välj sedan **Lägg till** och välj sedan rollen **DNS-zondeltagare.** Välj de användare eller grupper som krävs för att bevilja behörigheter.

![Azure RBAC på resursgruppsnivå via Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Behörigheter kan också beviljas [med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Azure RBAC på zonnivå

Azure RBAC-regler kan tillämpas på en prenumeration, en resursgrupp eller en enskild resurs. Den resursen kan vara en enskild DNS-zon eller en enskild postuppsättning.

Resursgruppen *myResourceGroup innehåller till exempel* zonen *contoso.com* och en underzon *customers.contoso.com*. CNAME-poster skapas för varje kundkonto. Administratörskontot som används för att hantera CNAME-poster tilldelas behörigheter för att skapa poster *customers.contoso.com zonen.* Kontot kan endast *hantera customers.contoso.com.*

Azure RBAC-behörigheter på zonnivå kan beviljas via Azure Portal.  Öppna **Åtkomstkontroll (IAM)** för zonen, välj Lägg till och välj sedan **rollen DNS-zondeltagare** och välj de användare eller grupper som krävs för att bevilja behörigheter.

![Azure RBAC på DNS-zonnivå via Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Behörigheter kan också beviljas [med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Postuppsättningsnivå För Azure RBAC

Behörigheter tillämpas på postuppsättningsnivå.  Användaren beviljas kontroll över poster som de behöver och kan inte göra några andra ändringar.

Azure RBAC-behörigheter på postuppsättningsnivå kan konfigureras via Azure Portal med hjälp **av knappen Access Control (IAM)** på sidan för postuppsättning:

![Postuppsättningsnivå för Azure RBAC via Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Azure RBAC-behörigheter på postuppsättningsnivå kan också [beviljas med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Anpassade roller

Den inbyggda rollen DNS-zondeltagare ger fullständig kontroll över en DNS-resurs. Det är möjligt att skapa egna anpassade Azure-roller för att ge mer begränsad kontroll.

Kontot som används för att hantera CNAME-poster beviljas endast behörighet att hantera CNAME-poster. Kontot kan inte ändra poster av andra typer. Kontot kan inte göra åtgärder på zonnivå, till exempel zon borttagning.

I följande exempel visas en anpassad rolldefinition för att endast hantera CNAME-poster:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
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

Egenskapen Actions definierar följande DNS-specifika behörigheter:

* `Microsoft.Network/dnsZones/CNAME/*` ger fullständig kontroll över CNAME-poster
* `Microsoft.Network/dnsZones/read` ger behörighet att läsa DNS-zoner, men inte ändra dem, så att du kan se den zon där CNAME skapas.

Återstående åtgärder kopieras från den inbyggda [rollen DNS-zondeltagare.](../role-based-access-control/built-in-roles.md#dns-zone-contributor)

> [!NOTE]
> Att använda en anpassad Azure-roll för att förhindra borttagning av postuppsättningar samtidigt som de fortfarande tillåts uppdateras är inte en effektiv kontroll. Det förhindrar att postuppsättningar tas bort, men det förhindrar inte att de ändras.  Tillåtna ändringar omfattar att lägga till och ta bort poster från postuppsättningen, inklusive borttagning av alla poster för att lämna en tom postuppsättning. Detta har samma effekt som när du tar bort postuppsättningen från en DNS-lösningssynvinkel.

Anpassade rolldefinitioner kan för närvarande inte definieras via Azure Portal. En anpassad roll som baseras på den här rolldefinitionen kan skapas med hjälp Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Det kan också skapas via Azure CLI:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Rollen kan sedan tilldelas på samma sätt som inbyggda roller, enligt beskrivningen tidigare i den här artikeln.

Mer information om hur du skapar, hanterar och tilldelar anpassade roller finns i Anpassade [roller i Azure.](../role-based-access-control/custom-roles.md)

## <a name="resource-locks"></a>Resurslås

Azure Resource Manager har stöd för en annan typ av säkerhetskontroll, möjligheten att låsa resurser. Resurslås tillämpas på resursen och är effektiva för alla användare och roller. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Det finns två typer av resurslås: **CanNotDelete** och **ReadOnly**. Dessa låstyper kan antingen tillämpas på en Privat DNS zon eller på en enskild postuppsättning. I följande avsnitt beskrivs flera vanliga scenarier och hur du stöder dem med hjälp av resurslås.

### <a name="protecting-against-all-changes"></a>Skydda mot alla ändringar

Du kan förhindra att ändringar görs genom att tillämpa ett ReadOnly-lås på zonen. Det här låset förhindrar att nya postuppsättningar skapas och befintliga postuppsättningar ändras eller tas bort.

Resurslås på zonnivå kan skapas via Azure Portal.  På sidan DNS-zon väljer du **Lås** och sedan **+Lägg till**:

![Resurslås på zonnivå via Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Resurslås på zonnivå kan också skapas via [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Motsvarande kommando är också [tillgängligt via Azure CLI:](/cli/azure/lock#az_lock_create)

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Skydda enskilda poster

Om du vill förhindra att en befintlig DNS-postuppsättning ändras använder du ett ReadOnly-lås för postuppsättningen.

> [!NOTE]
> Att tillämpa ett CanNotDelete-lås på en postuppsättning är inte en effektiv kontroll. Det förhindrar att postuppsättningen tas bort, men den förhindrar inte att den ändras.  Tillåtna ändringar omfattar att lägga till och ta bort poster från postuppsättningen, inklusive borttagning av alla poster för att lämna en tom postuppsättning. Detta har samma effekt som när du tar bort postuppsättningen från en DNS-lösningssyn.

Resurslås på postuppsättningsnivå kan för närvarande bara konfigureras med hjälp av Azure PowerShell.  De stöds inte i Azure Portal eller Azure CLI.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Skydda mot zonborttagning

När en zon tas bort i Azure DNS tas alla postuppsättningar i zonen bort.  Den här åtgärden kan inte ångras. Att ta bort en kritisk zon av misstag kan ha en betydande inverkan på verksamheten.  Det är viktigt att skydda mot oavsiktlig zonborttagning.

Genom att tillämpa ett CanNotDelete-lås på en zon förhindras zonen från att tas bort. Lås ärvs av underordnade resurser. Ett lås förhindrar att postuppsättningar i zonen tas bort. Enligt beskrivningen i anteckningen ovan är det ineffektivt eftersom poster fortfarande kan tas bort från de befintliga postuppsättningarna.

Alternativt kan du tillämpa ett CanNotDelete-lås på en postuppsättning i zonen, till exempel SOA-postuppsättningen. Zonen tas inte bort utan att även ta bort postuppsättningarna. Det här låset skyddar mot zonborttagning, samtidigt som postuppsättningar i zonen kan ändras fritt. Om ett försök görs att ta bort zonen Azure Resource Manager den här borttagningen. Borttagningen skulle också ta bort SOA-postuppsättningen, Azure Resource Manager blockerar anropet eftersom SOA är låst.  Inga postuppsättningar tas bort.

Följande PowerShell-kommando skapar ett CanNotDelete-lås mot SOA-posten för den angivna zonen:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Ett annat alternativ för att förhindra oavsiktlig zonborttagning är att använda en anpassad roll. Den här rollen säkerställer att de konton som används för att hantera zonerna inte har zon delete-behörigheter. 

När du behöver ta bort en zon kan du framtvinga en tvåstegs borttagning:

 - Först beviljar du borttagningsbehörigheter för zonen
 - För det andra beviljar du behörighet att ta bort zonen.

Den anpassade rollen fungerar för alla zoner som dessa konton använder. Konton med zon ta bort behörigheter, till exempel prenumerationens ägare, kan fortfarande ta bort en zon av misstag.

Det är möjligt att använda båda metoderna – resurslås och anpassade roller – samtidigt som en djupskyddsmetod för DNS-zonskydd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med Azure RBAC finns i [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/overview.md)
* Mer information om hur du arbetar med resurslås finns [i Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
