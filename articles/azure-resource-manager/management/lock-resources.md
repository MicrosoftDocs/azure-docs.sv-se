---
title: Lås resurser för att förhindra ändringar
description: Förhindra att användare uppdaterar eller tar bort Azure-resurser genom att använda ett lås för alla användare och roller.
ms.topic: conceptual
ms.date: 03/09/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d989f2077618ce80382b38acc651553cb331d5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932768"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Låsa resurser för att förhindra oväntade ändringar

Som administratör kan du låsa en prenumeration, resurs grupp eller resurs för att förhindra att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser. Låset åsidosätter alla behörigheter som användaren kan ha.

Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen kallas låsen **Delete** och **Read Only** .

* **CanNotDelete** innebär att behöriga användare fortfarande kan läsa och ändra en resurs, men de kan inte ta bort resursen.
* **ReadOnly** innebär att auktoriserade användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Att använda det här låset liknar att begränsa alla behöriga användare till de behörigheter som har beviljats av rollen **läsare** .

## <a name="how-locks-are-applied"></a>Hur låsen används

När du använder ett lås vid en överordnad omfattning ärver alla resurser inom den omfattningen samma lås. Även resurser som du lägger till senare ärver låset från det överordnade objektet. Det mest restriktiva låset i arv prioriteras.

Till skillnad från rollbaserad åtkomstkontroll använder du hanteringslås för att tillämpa en begränsning för alla användare och roller. Information om hur du anger behörigheter för användare och roller finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

Resource Manager-lås gäller endast för åtgärder som sker i hanteringsplanet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsar inte hur resurser utför sina egna funktioner. Resursändringar är begränsade, men resursåtgärder är inte begränsade. Ett skrivskyddat lås på en SQL Database logisk server förhindrar till exempel att du tar bort eller ändrar servern. Det hindrar dig inte från att skapa, uppdatera eller ta bort data i databaserna på den servern. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Att tänka på innan du använder lås

Att använda Lås kan leda till oväntade resultat eftersom vissa åtgärder som inte verkar ändra resursen verkligen kräver åtgärder som blockeras av låset. Lås förhindrar alla åtgärder som kräver en POST-begäran till Azure Resource Manager API. Några vanliga exempel på åtgärder som blockeras av lås är:

* Ett skrivskyddat lås på ett **lagrings konto** förhindrar att användare visar konto nycklarna. Åtgärden Azure Storage [list nycklar](/rest/api/storagerp/storageaccounts/listkeys) hanteras via en post-begäran för att skydda åtkomsten till konto nycklarna, som ger fullständig åtkomst till data i lagrings kontot. När ett skrivskyddat lås har kon figurer ATS för ett lagrings konto måste användare som inte har konto nycklar använda Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data. Ett skrivskyddat lås förhindrar också tilldelningen av Azure RBAC-roller som är begränsade till lagrings kontot eller till en data behållare (BLOB container eller queue).

* Ett borttagnings lås på ett **lagrings konto** förhindrar inte att data i det kontot tas bort eller ändras. Den här typen av lås skyddar bara själva lagrings kontot från att tas bort och skyddar inte BLOB-, Queue-, Table-eller File-data inom det lagrings kontot. 

* Ett skrivskyddat lås på ett **lagrings konto** förhindrar inte att data i det kontot tas bort eller ändras. Den här typen av lås skyddar bara själva lagrings kontot från att tas bort eller ändras och skyddar inte BLOB-, Queue-, tabell-, tabell-eller fildata i det lagrings kontot. 

* Ett skrivskyddat lås på en **App Service** resurs förhindrar att Visual Studio-Server Explorer visar filer för resursen, eftersom denna interaktion kräver skriv åtkomst.

* Ett skrivskyddat lås på en **resurs grupp** som innehåller en **virtuell dator** hindrar alla användare från att starta eller starta om den virtuella datorn. De här åtgärderna kräver en POST-begäran.

* Ett borttagnings lås för en **resurs grupp** förhindrar Azure Resource Manager från att [automatiskt ta bort distributioner](../templates/deployment-history-deletions.md) i historiken. Om du når 800-distributioner i historiken går det inte att distribuera.

* Ett borttagnings lås på **resurs gruppen** som skapats av **Azure Backup tjänsten** medför att säkerhets kopieringen Miss lyckas. Tjänsten har stöd för högst 18 återställnings punkter. När det är låst kan säkerhets kopierings tjänsten inte rensa återställnings punkter. Mer information finns i vanliga frågor och svar om hur du [säkerhetskopierar virtuella Azure-datorer](../../backup/backup-azure-vm-backup-faq.yml).

* Ett skrivskyddat lås på en **prenumeration** förhindrar att **Azure Advisor** fungerar korrekt. Advisor kan inte lagra resultatet av sina frågor.

## <a name="who-can-create-or-delete-locks"></a>Vem kan skapa eller ta bort lås

Om du vill skapa eller ta bort hanterings lås måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

## <a name="managed-applications-and-locks"></a>Hanterade program och lås

Vissa Azure-tjänster, till exempel Azure Databricks, använder [hanterade program](../managed-applications/overview.md) för att implementera tjänsten. I så fall skapar tjänsten två resurs grupper. En resurs grupp innehåller en översikt över tjänsten och är inte låst. Den andra resurs gruppen innehåller infrastrukturen för tjänsten och är låst.

Om du försöker ta bort infrastruktur resurs gruppen får du ett fel meddelande om att resurs gruppen är låst. Om du försöker ta bort låset för infrastruktur resurs gruppen får du ett fel meddelande om att låset inte kan tas bort eftersom det ägs av ett system program.

Ta i stället bort tjänsten, som också tar bort infrastruktur resurs gruppen.

För hanterade program väljer du den tjänst som du har distribuerat.

![Välj tjänst](./media/lock-resources/select-service.png)

Observera att tjänsten innehåller en länk till en **hanterad resurs grupp**. Resurs gruppen innehåller infrastrukturen och är låst. Den kan inte tas bort direkt.

![Visa hanterad grupp](./media/lock-resources/show-managed-group.png)

Om du vill ta bort allt för tjänsten, inklusive resurs gruppen låst infrastruktur, väljer du **ta bort** för tjänsten.

![Ta bort tjänst](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Konfigurera lås

### <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM-mall

När du använder en Azure Resource Manager-mall (ARM-mall) för att distribuera ett lås måste du vara medveten om omfattningen av låset och distributionens omfattning. Om du vill använda ett lås vid distributions omfånget, till exempel låsa en resurs grupp eller prenumeration, anger du inte egenskapen omfattning. När du låser en resurs i distributions omfånget anger du egenskapen omfattning.

Följande mall använder ett lås till resurs gruppen som den distribueras till. Observera att det inte finns någon omfattnings egenskap på Lås resursen eftersom låset matchar omfånget för distributionen. Den här mallen har distribuerats på resurs grupps nivå.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

Om du vill skapa en resurs grupp och låsa den distribuerar du följande mall på prenumerations nivån.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

När du använder ett lås på en **resurs** i resurs gruppen lägger du till egenskapen omfattning. Ange omfång till namnet på resursen som ska låsas.

I följande exempel visas en mall som skapar en app service-plan, en webbplats och ett lås på webbplatsen. Låsnings området är inställt på webbplatsen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Du låser distribuerade resurser med Azure PowerShell med kommandot [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Om du vill låsa en resurs anger du namnet på resursen, resurs typ och dess resurs grupp namn.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Ange namnet på resurs gruppen om du vill låsa en resurs grupp.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Om du vill ha information om ett lås använder du [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Använd följande för att hämta alla Lås i din prenumeration:

```azurepowershell-interactive
Get-AzResourceLock
```

Om du vill hämta alla Lås för en resurs använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla Lås för en resurs grupp använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås för en resurs använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Om du vill ta bort ett lås för en resurs grupp använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

Du låser distribuerade resurser med Azure CLI med hjälp av kommandot [AZ lock Create](/cli/azure/lock#az-lock-create) .

Om du vill låsa en resurs anger du namnet på resursen, resurs typ och dess resurs grupp namn.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Ange namnet på resurs gruppen om du vill låsa en resurs grupp.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Om du vill ha information om ett lås använder du [AZ lock List](/cli/azure/lock#az-lock-list). Använd följande för att hämta alla Lås i din prenumeration:

```azurecli
az lock list
```

Om du vill hämta alla Lås för en resurs använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla Lås för en resurs grupp använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås för en resurs använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Om du vill ta bort ett lås för en resurs grupp använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST-API

Du kan låsa distribuerade resurser med [REST API för hanterings lås](/rest/api/resources/managementlocks/managementlocks). Med REST API kan du skapa och ta bort lås och hämta information om befintliga lås.

Skapa ett lås genom att köra:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Omfånget kan vara en prenumeration, en resurs grupp eller en resurs. Namnet på låset är vad du vill för att anropa låset. Använd **2016-09-01** för API-version.

I begäran inkluderar du ett JSON-objekt som anger egenskaperna för låset.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du strukturerar resurserna logiskt finns i [använda taggar för att organisera dina resurser](tag-resources.md).
* Du kan tillämpa begränsningar och konventioner i din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
