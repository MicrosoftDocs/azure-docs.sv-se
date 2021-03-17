---
title: Flytta resurser till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1218df618f7f5fa0787505cb4fcee67dd264ea76
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601397"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Flytta resurser till en ny resursgrupp eller prenumeration

Den här artikeln visar hur du flyttar Azure-resurser till antingen en annan Azure-prenumeration eller en annan resurs grupp under samma prenumeration. Du kan använda Azure-portalen, Azure PowerShell, Azure CLI eller REST-API:et för att flytta resurser.

Både käll gruppen och mål gruppen är låsta under flytt åtgärden. Skriv- och borttagningsåtgärder blockeras för resursgrupperna tills flytten är klar. Det här låset innebär att du inte kan lägga till, uppdatera eller ta bort resurser i resurs grupperna. Det innebär inte att resurserna är låsta. Om du till exempel flyttar en SQL Server och dess databas till en ny resursgrupp sker inga avbrott för programmet som använder databasen. Det kan fortfarande läsa och skriva till databasen. Låset kan sista i högst fyra timmar, men de flesta flyttningar slutförs på mycket kortare tid.

Om du flyttar en resurs flyttas den bara till en ny resursgrupp eller prenumeration. Resursens plats ändras inte.

## <a name="checklist-before-moving-resources"></a>Checklista för att flytta resurser

Några viktiga steg måste utföras innan en resurs flyttas. Du kan undvika fel genom att verifiera dessa villkor.

1. De resurser som du vill flytta måste stödja flytt åtgärden. En lista över vilka resurser som stöder flytta finns i [Flytta åtgärds stöd för resurser](move-support-resources.md).

1. Vissa tjänster har särskilda begränsningar eller krav när du flyttar resurser. Om du flyttar någon av följande tjänster, kontrol lera att du har den vägledningen innan du flyttar.

   * Om du använder Azure Stack hubb kan du inte flytta resurser mellan grupper.
   * [Vägledning för App Services flytt](./move-limitations/app-service-move-limitations.md)
   * [Vägledning för flytt av Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klassisk distributions modell flytta vägledning](./move-limitations/classic-model-move-limitations.md) – klassisk beräkning, klassisk lagring, klassiska virtuella nätverk och Cloud Services
   * [Vägledning om nätverks flytt](./move-limitations/networking-move-limitations.md)
   * [Vägledning för Recovery Services flytt](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Vägledning för Virtual Machines flytt](./move-limitations/virtual-machines-move-limitations.md)
   * Information om hur du flyttar en Azure-prenumeration till en ny hanterings grupp finns i [Flytta prenumerationer](../../governance/management-groups/manage.md#move-subscriptions).

1. Om du flyttar en resurs som har en Azure-roll som tilldelats direkt till resursen (eller en underordnad resurs) flyttas inte roll tilldelningen och blir överbliven. När du har flyttat måste du återskapa roll tilldelningen. Slutligen tas den överblivna roll tilldelningen bort automatiskt, men det är en bra idé att ta bort roll tilldelningen innan du flyttar resursen.

    Information om hur du hanterar roll tilldelningar finns i [lista Azure Role-tilldelningar](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) och [tilldela Azure-roller](../../role-based-access-control/role-assignments-portal.md).

1. Käll-och mål prenumerationerna måste vara aktiva. Om du har problem med att aktivera ett konto som har inaktiverats [skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Välj **prenumerations hantering** för typ av problem.

1. Käll-och mål prenumerationerna måste finnas inom samma [Azure Active Directory-klient](../../active-directory/develop/quickstart-create-new-tenant.md). Om du vill kontrol lera att båda prenumerationerna har samma klient-ID använder Azure PowerShell eller Azure CLI.

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

   Om klient-ID: na för käll-och mål prenumerationerna inte är samma använder du följande metoder för att stämma av klient-ID: n:

   * [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Så här associerar du eller lägger till en prenumeration i din Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Annars visas ett fel meddelande om att **prenumerationen inte är registrerad för en resurs typ**. Du kanske ser det här felet när du flyttar en resurs till en ny prenumeration, men prenumerationen har aldrig använts med den resurs typen.

   För PowerShell använder du följande kommandon för att hämta registrerings status:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Om du vill registrera en resurs leverantör använder du:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   För Azure CLI använder du följande kommandon för att hämta registrerings status:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Om du vill registrera en resurs leverantör använder du:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Kontot som flyttar resurserna måste minst ha följande behörigheter:

   * **Microsoft. Resources/Subscriptions/resourceGroups/moveResources/åtgärd** för käll resurs gruppen.
   * **Microsoft. Resources/Subscriptions/resourceGroups/Write** på mål resurs gruppen.

1. Innan du flyttar resurserna kontrollerar du prenumerations kvoterna för den prenumeration som du flyttar resurserna till. Om du flyttar resurserna kommer prenumerationen att överskrida sina gränser, men du måste kontrol lera om du kan begära en ökning av kvoten. En lista över gränser och hur du begär en ökning finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **För att flytta över prenumerationer måste resursen och dess beroende resurser finnas i samma resurs grupp och de måste flyttas tillsammans.** En virtuell dator med Managed disks skulle till exempel kräva att den virtuella datorn och de hanterade diskarna flyttas tillsammans, tillsammans med andra beroende resurser.

   Om du flyttar en resurs till en ny prenumeration kan du kontrol lera om resursen har resurser som är beroende av varandra och om de finns i samma resurs grupp. Om resurserna inte finns i samma resurs grupp kontrollerar du om resurserna kan kombineras till samma resurs grupp. I så fall kan du ta med alla dessa resurser i samma resurs grupp genom att använda en flyttnings åtgärd över resurs grupper.

   Mer information finns i [scenario för att flytta mellan prenumerationer](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenario för att flytta mellan prenumerationer

Att flytta resurser från en prenumeration till en annan är en process i tre steg:

![flytt scenario mellan prenumerationer](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

I illustrations syfte har vi bara en beroende resurs.

* Steg 1: om beroende resurser fördelas mellan olika resurs grupper måste du först flytta dem till en resurs grupp.
* Steg 2: flytta resurs-och beroende resurser tillsammans från käll prenumerationen till mål prenumerationen.
* Steg 3: du kan också distribuera om de beroende resurserna till olika resurs grupper inom mål prenumerationen.

## <a name="validate-move"></a>Verifiera flytt

Med [åtgärden verifiera flytt](/rest/api/resources/resources/validatemoveresources) kan du testa flytt scenariot utan att faktiskt flytta resurserna. Använd den här åtgärden för att kontrol lera om flytten kommer att lyckas. Verifiering anropas automatiskt när du skickar en flyttnings förfrågan. Använd endast den här åtgärden när du måste för att fastställa resultaten. Om du vill köra den här åtgärden behöver du:

* namnet på käll resurs gruppen
* resurs-ID för mål resurs gruppen
* resurs-ID för varje resurs som ska flyttas
* åtkomsttoken [för ditt](/rest/api/azure/#acquire-an-access-token) konto

Skicka följande begäran:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Med en begär ande text:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om begäran har formaterats korrekt returnerar åtgärden:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Status koden 202 visar att verifierings förfrågan godtogs, men den har ännu inte fastställts om flytt åtgärden lyckas. `location`Värdet innehåller en URL som du använder för att kontrol lera status för den tids krävande åtgärden.  

Skicka följande begäran för att kontrol lera statusen:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Även om åtgärden fortfarande körs fortsätter du att ta emot status koden 202. Vänta antalet sekunder som anges i `retry-after` värdet innan du försöker igen. Om flytt åtgärden verifieras visas status koden 204. Om det inte går att flytta verifieringen visas ett fel meddelande, till exempel:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Använda portalen

Om du vill flytta resurser väljer du den resurs grupp som innehåller resurserna.

När du visar resurs gruppen är alternativet flytta inaktiverat.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="flytt alternativet är inaktiverat":::

Om du vill aktivera alternativet flytta väljer du de resurser som du vill flytta. Markera kryss rutan längst upp i listan om du vill välja alla resurser. Eller Välj resurser individuellt. När du har valt resurser är alternativet flytta aktiverat.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="Välj resurser":::

Välj knappen **Flytta** .

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="flytta alternativ":::

Med den här knappen kan du välja mellan tre alternativ:

* Flytta till en ny resurs grupp.
* Flytta till en ny prenumeration.
* Flytta till en ny region. Om du vill ändra regioner, se [Flytta resurser mellan regioner (från resurs grupp)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json).

Välj om du vill flytta resurserna till en ny resurs grupp eller till en ny prenumeration.

Välj mål resurs grupp. Bekräfta att du behöver uppdatera skript för dessa resurser och välj **OK**. Om du har valt att flytta till en ny prenumeration måste du också välja mål prenumerationen.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="Välj mål":::

När du har verifierat att resurserna kan flyttas visas ett meddelande om att flytt åtgärden körs.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="indikering":::

När du är klar får du ett meddelande om resultatet.

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill flytta befintliga resurser till en annan resurs grupp eller prenumeration använder du kommandot [Move-AzResource](/powershell/module/az.resources/move-azresource) . I följande exempel visas hur du flyttar flera resurser till en ny resurs grupp.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en ny prenumeration anger du ett värde för `DestinationSubscriptionId` parametern.

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

Om du vill flytta befintliga resurser till en annan resurs grupp eller prenumeration använder du kommandot [AZ Resource Move](/cli/azure/resource#az-resource-move) . Ange resurs-ID för de resurser som ska flyttas. I följande exempel visas hur du flyttar flera resurser till en ny resurs grupp. I `--ids` -parametern anger du en blankstegsavgränsad lista över resurs-ID: n som ska flyttas.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Ange parametern om du vill flytta till en ny prenumeration `--destination-subscription-id` .

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="use-rest-api"></a>Använda REST-API

Om du vill flytta befintliga resurser till en annan resurs grupp eller prenumeration använder du åtgärden [Flytta resurser](/rest/api/resources/Resources/MoveResources) .

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

I begär ande texten anger du mål resurs gruppen och de resurser som ska flyttas.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga: min resurs flytt åtgärd, som vanligt vis tar några minuter, har körts i nästan en timme. Finns det något fel?**

Att flytta en resurs är en komplex åtgärd som har olika faser. Den kan omfatta mer än bara resurs leverantören av resursen som du försöker flytta. På grund av beroenden mellan resurs leverantörerna kan Azure Resource Manager utföra 4 timmar för att åtgärden ska slutföras. Den här tids perioden ger resurs leverantörer en chans att återställa från tillfälliga problem. Om din flyttnings förfrågan ligger inom fyra timmars period fortsätter åtgärden att utföras och kan fortfarande lyckas. Käll-och mål resurs grupperna är låsta under den här tiden för att undvika konsekvens problem.

**Fråga: Varför är min resurs grupp låst i fyra timmar när resursen flyttas?**

En move-begäran kan vara högst fyra timmar att slutföra. För att förhindra ändringar av de resurser som flyttas, är både käll-och mål resurs grupperna låsta under hela resurs flytten.

Det finns två faser i en flyttnings förfrågan. I den första fasen flyttas resursen. I den andra fasen skickas meddelanden till andra resurs leverantörer som är beroende av den resurs som flyttas. En resurs grupp kan låsas i hela fyra timmar när en resurs leverantör Miss lyckas med någon av de två faserna. Under den tillåtna tiden försöker Resource Manager att försöka utföra åtgärden igen.

Om en resurs inte kan flyttas inom fyra timmar låser Resource Manager både resurs grupper. Resurser som har flyttats är i mål resurs gruppen. Resurser som inte gick att flytta finns kvar i käll resurs gruppen.

**Fråga: vilka konsekvenser för käll-och mål resurs grupper som låses när resursen flyttas?**

Låset förhindrar att du tar bort någon av resurs grupperna, skapar en ny resurs i antingen resurs gruppen eller tar bort någon av de resurser som ingår i flytten.

Följande bild visar ett fel meddelande från Azure Portal när en användare försöker ta bort en resurs grupp som är en del av en pågående flytt.

![Flytta fel meddelande som försöker ta bort](./media/move-resource-group-and-subscription/move-error-delete.png)

**Fråga: Vad betyder felkoden "MissingMoveDependentResources"?**

När du flyttar en resurs måste dess beroende resurser antingen finnas i mål resurs gruppen eller prenumerationen, eller inkluderas i flyttnings förfrågan. Du får MissingMoveDependentResources-felkoden när en beroende resurs inte uppfyller det här kravet. Fel meddelandet innehåller information om den beroende resurs som måste tas med i flyttnings förfrågan.

Om du till exempel flyttar en virtuell dator kan du behöva flytta sju resurs typer till tre olika resurs leverantörer. Dessa resurs leverantörer och typer är:

* Microsoft.Compute

  * virtualMachines
  * disk
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Ett annat vanligt exempel är att flytta ett virtuellt nätverk. Du kan behöva flytta flera andra resurser som är kopplade till det virtuella nätverket. Move-begäran kan behöva flytta offentliga IP-adresser, väg tabeller, virtuella nätverks-gatewayer, nätverks säkerhets grupper och andra.

**Fråga: Varför kan jag inte flytta vissa resurser i Azure?**

För närvarande går det inte att flytta alla resurser i Azure-supporten. En lista över resurser som stöder flytta finns i avsnittet [Flytta åtgärds stöd för resurser](move-support-resources.md).

## <a name="next-steps"></a>Nästa steg

En lista över vilka resurser som stöder flytta finns i [Flytta åtgärds stöd för resurser](move-support-resources.md).
