---
title: Flytta resurser till en ny prenumeration eller resursgrupp
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 04/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08f2c123d37ac992e926e983d59edc650a8ab7ef
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728310"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Flytta resurser till en ny resursgrupp eller prenumeration

Den här artikeln visar hur du flyttar Azure-resurser till antingen en annan Azure-prenumeration eller en annan resursgrupp under samma prenumeration. Du kan använda Azure-portalen, Azure PowerShell, Azure CLI eller REST-API:et för att flytta resurser.

Både källgruppen och målgruppen låses under flyttåtgärden. Skriv- och borttagningsåtgärder blockeras för resursgrupperna tills flytten är klar. Det här låset innebär att du inte kan lägga till, uppdatera eller ta bort resurser i resursgrupperna. Det innebär inte att resurserna är låsta. Om du till exempel flyttar en Azure SQL logisk server och dess databaser till en ny resursgrupp eller prenumeration, upplever program som använder databaserna ingen avbrottstid. De kan fortfarande läsa och skriva till databaserna. Låset kan vara i högst fyra timmar, men de flesta förflyttningar slutförs på mycket kortare tid.

Om du flyttar en resurs flyttas den bara till en ny resursgrupp eller prenumeration. Resursens plats ändras inte.

## <a name="changed-resource-id"></a>Resurs-ID har ändrats

När du flyttar en resurs ändrar du dess resurs-ID. Standardformatet för ett resurs-ID är `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . När du flyttar en resurs till en ny resursgrupp eller prenumeration ändrar du ett eller flera värden i sökvägen.

Om du använder resurs-ID:t var som helst måste du ändra det värdet. Om du till exempel har en [anpassad instrumentpanel](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) i portalen som refererar till ett resurs-ID måste du uppdatera det värdet. Leta efter skript eller mallar som behöver uppdateras för det nya resurs-ID:t.

## <a name="checklist-before-moving-resources"></a>Checklista för att flytta resurser

Några viktiga steg måste utföras innan en resurs flyttas. Du kan undvika fel genom att verifiera dessa villkor.

1. De resurser som du vill flytta måste ha stöd för flyttåtgärden. En lista över vilka resurser som stöder flytt finns i [Stöd för flytt av resurser.](move-support-resources.md)

1. Vissa tjänster har specifika begränsningar eller krav när du flyttar resurser. Om du flyttar någon av följande tjänster bör du kontrollera den vägledningen innan du flyttar.

   * Om du använder Azure Stack Hub kan du inte flytta resurser mellan grupper.
   * [App Services flyttvägledning](./move-limitations/app-service-move-limitations.md)
   * [Flyttvägledning för Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Vägledning för flytt av klassisk distributionsmodell](./move-limitations/classic-model-move-limitations.md) – klassisk beräkning, klassisk lagring, klassiska virtuella nätverk och Cloud Services
   * [Vägledning för nätverksflyttning](./move-limitations/networking-move-limitations.md)
   * [Flyttvägledning för Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtual Machines flyttvägledning](./move-limitations/virtual-machines-move-limitations.md)
   * Information om hur du flyttar en Azure-prenumeration till en ny hanteringsgrupp finns [i Flytta prenumerationer.](../../governance/management-groups/manage.md#move-subscriptions)

1. Om du flyttar en resurs som har en Azure-roll som tilldelats direkt till resursen (eller en underordnad resurs), flyttas inte rolltilldelningen och blir överbliven. Efter flytten måste du skapa rolltilldelningen på nytt. Slutligen tas den överblivna rolltilldelningen bort automatiskt, men vi rekommenderar att du tar bort rolltilldelningen innan flytten.

    Information om hur du hanterar rolltilldelningar finns i Lista [över Azure-rolltilldelningar](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) och [Tilldela Azure-roller.](../../role-based-access-control/role-assignments-portal.md)

1. Käll- och målprenumerationer måste vara aktiva. Om du har problem med att aktivera ett konto som har inaktiverats skapar du [en Azure-supportbegäran.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Välj **Prenumerationshantering** som typ av problem.

1. Käll- och målprenumerationer måste finnas inom [samma Azure Active Directory klientorganisation](../../active-directory/develop/quickstart-create-new-tenant.md). Om du vill kontrollera att båda prenumerationerna har samma klientorganisations-ID använder Azure PowerShell eller Azure CLI.

   För Azure PowerShell använder du:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Om du använder Azure CLI använder du:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Om klientorganisations-ID:erna för käll- och målprenumerationer inte är desamma använder du följande metoder för att stämma av klient-ID:erna:

   * [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Så här associerar du eller lägger till en prenumeration i din Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Om inte får du ett felmeddelande om att **prenumerationen inte har registrerats för en resurstyp**. Du kan se det här felet när du flyttar en resurs till en ny prenumeration, men prenumerationen har aldrig använts med den resurstypen.

   För PowerShell använder du följande kommandon för att hämta registreringsstatusen:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Om du vill registrera en resursprovider använder du:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   För Azure CLI använder du följande kommandon för att hämta registreringsstatusen:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Om du vill registrera en resursprovider använder du:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Kontot som flyttar resurserna måste ha minst följande behörigheter:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** i källresursgruppen.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** på målresursgruppen.

1. Innan du flyttar resurserna kontrollerar du prenumerationskvoterna för den prenumeration som du flyttar resurserna till. Om flytt av resurserna innebär att prenumerationen överskrider sina gränser måste du kontrollera om du kan begära en ökning av kvoten. En lista över begränsningar och hur du begär en ökning finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

1. **För en flytt mellan prenumerationer måste resursen och dess beroende resurser finnas i samma resursgrupp och de måste flyttas tillsammans.** En virtuell dator med hanterade diskar skulle till exempel kräva att den virtuella datorn och de hanterade diskarna flyttas tillsammans, tillsammans med andra beroende resurser.

   Om du flyttar en resurs till en ny prenumeration kontrollerar du om resursen har några beroende resurser och om den finns i samma resursgrupp. Om resurserna inte finns i samma resursgrupp kontrollerar du om resurserna kan kombineras till samma resursgrupp. I så fall kan du föra in alla dessa resurser i samma resursgrupp genom att använda en flyttåtgärd mellan resursgrupper.

   Mer information finns i [Scenario för flytt mellan prenumerationer.](#scenario-for-move-across-subscriptions)

## <a name="scenario-for-move-across-subscriptions"></a>Scenario för att flytta mellan prenumerationer

Att flytta resurser från en prenumeration till en annan är en trestegsprocess:

![flyttscenario mellan prenumerationer](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

I illustrationssyfte har vi bara en beroende resurs.

* Steg 1: Om beroende resurser distribueras mellan olika resursgrupper flyttar du dem först till en resursgrupp.
* Steg 2: Flytta resursen och beroende resurser tillsammans från källprenumerationen till målprenumerationen.
* Steg 3: Om du vill kan du distribuera om de beroende resurserna till olika resursgrupper i målprenumerationen.

## <a name="validate-move"></a>Verifiera flytt

Med [åtgärden för att validera](/rest/api/resources/resources/moveresources) flytten kan du testa flyttscenariot utan att faktiskt flytta resurserna. Använd den här åtgärden för att kontrollera om flytten lyckas. Validering anropas automatiskt när du skickar en flyttbegäran. Använd bara den här åtgärden när du behöver fördefiniera resultatet. Om du vill köra den här åtgärden behöver du följande:

* namnet på källresursgruppen
* resurs-ID för målresursgruppen
* resurs-ID för varje resurs som ska flyttas
* [åtkomsttoken](/rest/api/azure/#acquire-an-access-token) för ditt konto

Skicka följande begäran:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Med en begärandetext:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om begäran är korrekt formaterad returnerar åtgärden:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202-statuskoden anger att valideringsbegäran godkändes, men den har ännu inte fastställt om flyttåtgärden kommer att lyckas. Värdet `location` innehåller en URL som du använder för att kontrollera status för den långvariga åtgärden.

Om du vill kontrollera statusen skickar du följande begäran:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

När åtgärden fortfarande körs fortsätter du att få statuskoden 202. Vänta det antal sekunder som anges i värdet `retry-after` innan du försöker igen. Om flyttåtgärden verifieras får du statuskoden 204. Om flyttverifieringen misslyckas visas ett felmeddelande, till exempel:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Använda portalen

Om du vill flytta resurser väljer du den resursgrupp som innehåller resurserna.

När du visar resursgruppen inaktiveras flyttalternativet.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="flytta alternativet inaktiverat":::

Om du vill aktivera flyttalternativet väljer du de resurser som du vill flytta. Markera alla resurser genom att markera kryssrutan överst i listan. Du kan också välja resurser individuellt. När du har valt resurser aktiveras flyttalternativet.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="välj resurser":::

Välj **knappen** Flytta.

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="flytta alternativ":::

Den här knappen ger dig tre alternativ:

* Flytta till en ny resursgrupp.
* Flytta till en ny prenumeration.
* Flytta till en ny region. Information om hur du ändrar regioner [finns i Flytta resurser mellan regioner (från resursgrupp).](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)

Välj om du vill flytta resurserna till en ny resursgrupp eller en ny prenumeration.

Välj målresursgruppen. Bekräfta att du behöver uppdatera skripten för dessa resurser och välj **OK.** Om du har valt att flytta till en ny prenumeration måste du också välja målprenumerationen.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="välj mål":::

När du har verifierat att resurserna kan flyttas visas ett meddelande om att flyttåtgärden körs.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="Anmälan":::

När det är klart meddelas du om resultatet.

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill flytta befintliga resurser till en annan resursgrupp eller prenumeration använder du kommandot [Move-AzResource.](/powershell/module/az.resources/move-azresource) I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en ny prenumeration inkluderar du ett värde för `DestinationSubscriptionId` parametern .

## <a name="use-azure-cli"></a>Använda Azure CLI

Om du vill flytta befintliga resurser till en annan resursgrupp eller prenumeration använder du [kommandot az resource move.](/cli/azure/resource#az-resource-move) Ange resurs-ID:erna för de resurser som ska flyttas. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp. I `--ids` parametern anger du en blankstegsavgränsad lista över de resurs-ID:er som ska flyttas.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Om du vill flytta till en ny prenumeration anger du `--destination-subscription-id` parametern .

## <a name="use-rest-api"></a>Använda REST-API

Om du vill flytta befintliga resurser till en annan resursgrupp eller prenumeration använder du [åtgärden Flytta](/rest/api/resources/resources/moveresources) resurser.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

I begärandetexten anger du målresursgruppen och de resurser som ska flyttas.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga: Min resursflyttningsåtgärd, som vanligtvis tar några minuter, har körts i nästan en timme. Är det något fel?**

Att flytta en resurs är en komplex åtgärd som har olika faser. Det kan omfatta mer än bara resursprovidern för den resurs som du försöker flytta. På grund av beroenden mellan resursproviders kan Azure Resource Manager 4 timmar innan åtgärden slutförs. Den här tidsperioden ger resursproviders möjlighet att återställa från tillfälliga problem. Om din flyttbegäran inträder inom fyra timmar fortsätter åtgärden att försöka slutföras och kan fortfarande lyckas. Käll- och målresursgrupperna är låsta under den här tiden för att undvika konsekvensproblem.

**Fråga: Varför är min resursgrupp låst i fyra timmar under resursflyttningen?**

En flyttbegäran tillåts högst fyra timmar att slutföra. För att förhindra att ändringar av resurserna flyttas låses både käll- och målresursgrupperna under resursflyttet.

Det finns två faser i en flyttbegäran. I den första fasen flyttas resursen. I den andra fasen skickas meddelanden till andra resursproviders som är beroende av den resurs som flyttas. En resursgrupp kan låsas under hela fyra timmar när en resursprovider misslyckas i någon av faserna. Under den tillåtna tiden Resource Manager det misslyckade steget.

Om en resurs inte kan flyttas inom fyra timmar Resource Manager båda resursgrupperna. Resurser som har flyttats finns i målresursgruppen. Resurser som inte kunde flyttas lämnas kvar i källresursgruppen.

**Fråga: Vilka är konsekvenserna av att käll- och målresursgrupperna låses under resursflyttningen?**

Låset förhindrar att du tar bort antingen resursgruppen, skapar en ny resurs i antingen resursgruppen eller tar bort någon av de resurser som är inblandade i flytten.

I följande bild visas ett felmeddelande från Azure Portal när en användare försöker ta bort en resursgrupp som är en del av en pågående flytt.

![Flytta felmeddelande som försöker ta bort](./media/move-resource-group-and-subscription/move-error-delete.png)

**Fråga: Vad betyder felkoden "MissingMoveDependentResources"?**

När du flyttar en resurs måste dess beroende resurser antingen finnas i målresursgruppen eller prenumerationen eller ingå i flyttbegäran. Du får felkoden MissingMoveDependentResources när en beroende resurs inte uppfyller det här kravet. Felmeddelandet innehåller information om den beroende resurs som måste ingå i flyttbegäran.

Att flytta en virtuell dator kan till exempel kräva att sju resurstyper flyttas med tre olika resursproviders. Dessa resursproviders och resurstyper är:

* Microsoft.Compute

  * virtualMachines
  * Diskar
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Ett annat vanligt exempel är att flytta ett virtuellt nätverk. Du kan behöva flytta flera andra resurser som är associerade med det virtuella nätverket. Flyttbegäran kan kräva flytt av offentliga IP-adresser, vägtabeller, virtuella nätverksgatewayer, nätverkssäkerhetsgrupper med mera.

**Fråga: Varför kan jag inte flytta vissa resurser i Azure?**

För närvarande är det inte alla resurser i Azure som har stöd för flytt. En lista över resurser som stöder flytt finns i [Stöd för flytt av resurser.](move-support-resources.md)

**Fråga: Hur många resurser kan jag flytta i en enda åtgärd?**

När det är möjligt kan du dela upp stora förflyttningar i separata flyttåtgärder. Resource Manager returnerar omedelbart ett fel när det finns fler än 800 resurser i en enda åtgärd. Men att flytta mindre än 800 resurser kan också misslyckas genom att en tidsinställning går ut.

**Fråga: Vad innebär felet att en resurs inte är i tillståndet lyckades?**

När du får ett felmeddelande som anger att en resurs inte kan flyttas eftersom den inte är i ett lyckades-tillstånd kan det faktiskt vara en beroende resurs som blockerar flytten. Felkoden är vanligtvis **MoveCannotProceedWithResourcesNotInSucceededState.**

Om käll- eller målresursgruppen innehåller ett virtuellt nätverk kontrolleras tillstånden för alla beroende resurser för det virtuella nätverket under flytten. Kontrollen omfattar dessa resurser direkt och indirekt beroende av det virtuella nätverket. Om någon av dessa resurser är i ett misslyckat tillstånd blockeras flytten. Om till exempel en virtuell dator som använder det virtuella nätverket har misslyckats blockeras flytten. Flytten blockeras även om den virtuella datorn inte är en av de resurser som flyttas och inte finns i någon av resursgrupperna för flytten.

När du får det här felet har du två alternativ. Flytta antingen dina resurser till en resursgrupp som inte har något virtuellt nätverk eller [kontakta supporten.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Nästa steg

En lista över vilka resurser som stöder flytt finns i [Stöd för flytt av resurser.](move-support-resources.md)
