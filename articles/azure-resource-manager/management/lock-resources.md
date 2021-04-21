---
title: Låsa resurser för att förhindra ändringar
description: Förhindra användare från att uppdatera eller ta bort Azure-resurser genom att tillämpa ett lås för alla användare och roller.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71637318a60e66bf5000de2f564d740cc101cc60
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768731"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Låsa resurser för att förhindra oväntade ändringar

Som administratör kan du låsa en prenumeration, resursgrupp eller resurs för att förhindra att andra användare i din organisation oavsiktligt tar bort eller ändrar viktiga resurser. Låset åsidosätter alla behörigheter som användaren kan ha.

Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen kallas låsen Ta **bort** **respektive** Skrivskyddade.

* **CanNotDelete** innebär att behöriga användare fortfarande kan läsa och ändra en resurs, men de kan inte ta bort resursen.
* **ReadOnly** innebär att behöriga användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Att tillämpa det här låset liknar att begränsa alla behöriga användare till de behörigheter som beviljas av **rollen** Läsare.

## <a name="how-locks-are-applied"></a>Hur lås tillämpas

När du tillämpar ett lås i ett överordnat omfång ärver alla resurser inom det omfånget samma lås. Även resurser som du lägger till senare ärver låset från den överordnade. Det mest restriktiva låset i arvet har företräde.

Till skillnad från rollbaserad åtkomstkontroll använder du hanteringslås för att tillämpa en begränsning för alla användare och roller. Mer information om hur du anger behörigheter för användare och roller finns [i Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)

Resource Manager-lås gäller endast för åtgärder som sker i hanteringsplanet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsar inte hur resurser utför sina egna funktioner. Resursändringar är begränsade, men resursåtgärder är inte begränsade. Ett ReadOnly-lås på en logisk SQL Database hindrar dig från att ta bort eller ändra servern. Det hindrar dig inte från att skapa, uppdatera eller ta bort data i databaserna på den servern. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Att tänka på innan du använder lås

Att tillämpa lås kan leda till oväntade resultat eftersom vissa åtgärder som inte verkar ändra resursen faktiskt kräver åtgärder som blockeras av låset. Lås förhindrar åtgärder som kräver en POST-begäran till Azure Resource Manager API. Några vanliga exempel på åtgärder som blockeras av lås är:

* Ett skrivskyddade lås för ett **lagringskonto** hindrar användare från att visa kontonycklarna. Åtgärden Azure Storage [listnycklar](/rest/api/storagerp/storageaccounts/listkeys) hanteras via en POST-begäran för att skydda åtkomsten till kontonycklarna, som ger fullständig åtkomst till data i lagringskontot. När ett skrivskyddat lås har konfigurerats för ett lagringskonto måste användare som inte har kontonycklarna använda Azure AD-autentiseringsuppgifter för att få åtkomst till blob- eller ködata. Ett skrivskyddat lås förhindrar också tilldelning av Azure RBAC-roller som är begränsade till lagringskontot eller till en datacontainer (blobcontainer eller kö).

* Ett lås för att inte ta bort **på ett lagringskonto** förhindrar inte att data i det kontot tas bort eller ändras. Den här typen av lås skyddar endast själva lagringskontot från att tas bort och skyddar inte blob-, kö-, tabell- eller fildata i det lagringskontot. 

* Ett skrivskyddat lås för **ett lagringskonto** förhindrar inte att data i det kontot tas bort eller ändras. Den här typen av lås skyddar endast själva lagringskontot från att tas bort eller ändras och skyddar inte blob-, kö-, tabell- eller fildata i det lagringskontot. 

* Ett skrivskyddade lås på en **App Service** hindrar Visual Studio Server Explorer att visa filer för resursen eftersom den interaktionen kräver skrivåtkomst.

* Ett skrivskyddad lås på **en resursgrupp** som innehåller **App Service plan** förhindrar att [du skalar upp eller ut planen](../../app-service/manage-scale-up.md).

* Ett skrivskyddade lås på en **resursgrupp som** innehåller en **virtuell dator** hindrar alla användare från att starta eller starta om den virtuella datorn. Dessa åtgärder kräver en POST-begäran.

* Ett lås för att inte ta bort **en resursgrupp** förhindrar Azure Resource Manager automatiskt [tar bort distributioner](../templates/deployment-history-deletions.md) i historiken. Om du når 800 distributioner i historiken misslyckas dina distributioner.

* Det går inte att ta bort **låset för resursgruppen** **som skapats Azure Backup Service** gör att säkerhetskopieringar misslyckas. Tjänsten stöder högst 18 återställningspunkter. När säkerhetskopieringstjänsten är låst kan den inte rensa återställningspunkter. Mer information finns i [Vanliga frågor och svar – Back up Azure VMs (](../../backup/backup-azure-vm-backup-faq.yml)Back up Azure VMs ).

* Ett skrivskyddade lås för en **prenumeration** **förhindrar Azure Advisor** fungerar korrekt. Advisor kan inte lagra resultatet av sina frågor.

## <a name="who-can-create-or-delete-locks"></a>Vem kan skapa eller ta bort lås

Om du vill skapa eller ta bort hanteringslås måste du ha åtkomst `Microsoft.Authorization/*` till eller `Microsoft.Authorization/locks/*` åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

## <a name="managed-applications-and-locks"></a>Hanterade program och lås

Vissa Azure-tjänster, till Azure Databricks, använder [hanterade program](../managed-applications/overview.md) för att implementera tjänsten. I så fall skapar tjänsten två resursgrupper. En resursgrupp innehåller en översikt över tjänsten och är inte låst. Den andra resursgruppen innehåller infrastrukturen för tjänsten och är låst.

Om du försöker ta bort resursgruppen för infrastrukturen visas ett felmeddelande om att resursgruppen är låst. Om du försöker ta bort låset för infrastrukturresursgruppen visas ett felmeddelande om att låset inte kan tas bort eftersom det ägs av ett systemprogram.

Ta i stället bort tjänsten, som även tar bort resursgruppen infrastruktur.

För hanterade program väljer du den tjänst som du har distribuerat.

![Välj tjänst](./media/lock-resources/select-service.png)

Observera att tjänsten innehåller en länk för en **hanterad resursgrupp**. Resursgruppen innehåller infrastrukturen och är låst. Den kan inte tas bort direkt.

![Visa hanterad grupp](./media/lock-resources/show-managed-group.png)

Om du vill ta bort allt för tjänsten, inklusive resursgruppen för låst infrastruktur, väljer **du Ta** bort för tjänsten.

![Ta bort tjänst](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Konfigurera lås

### <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM-mall

När du använder Azure Resource Manager en mall (ARM-mall) för att distribuera ett lås måste du vara medveten om omfånget för låset och omfånget för distributionen. Om du vill låsa distributionsomfånget, till exempel låsa en resursgrupp eller prenumeration, ska du inte ange omfångsegenskapen. När du låser en resurs i distributionsomfånget anger du omfångsegenskapen.

Följande mall tillämpar ett lås på resursgruppen som den distribueras till. Observera att det inte finns någon omfångsegenskap för låsresursen eftersom omfånget för låset matchar omfånget för distributionen. Den här mallen distribueras på resursgruppsnivå.

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

Om du vill skapa en resursgrupp och låsa den distribuerar du följande mall på prenumerationsnivå.

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

När du använder ett lås för en **resurs** i resursgruppen lägger du till omfångsegenskapen. Ange omfånget till namnet på resursen som ska låsas.

I följande exempel visas en mall som skapar en App Service-plan, en webbplats och ett lås på webbplatsen. Omfånget för låset är inställt på webbplatsen.

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

Du låser distribuerade resurser med Azure PowerShell med hjälp av [kommandot New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Om du vill låsa en resurs anger du namnet på resursen, dess resurstyp och dess resursgruppnamn.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill låsa en resursgrupp anger du namnet på resursgruppen.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Om du vill ha information om ett lås använder [du Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Om du vill hämta alla lås i din prenumeration använder du:

```azurepowershell-interactive
Get-AzResourceLock
```

Om du vill hämta alla lås för en resurs använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla lås för en resursgrupp använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås för en resurs använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Om du vill ta bort ett lås för en resursgrupp använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

Du låser distribuerade resurser med Azure CLI med hjälp av [kommandot az lock create.](/cli/azure/lock#az_lock_create)

Om du vill låsa en resurs anger du namnet på resursen, dess resurstyp och dess resursgruppsnamn.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Om du vill låsa en resursgrupp anger du namnet på resursgruppen.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Om du vill ha information om ett lås använder [du az lock list](/cli/azure/lock#az_lock_list). Om du vill hämta alla lås i din prenumeration använder du:

```azurecli
az lock list
```

Om du vill hämta alla lås för en resurs använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla lås för en resursgrupp använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås för en resurs använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Om du vill ta bort ett lås för en resursgrupp använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST-API

Du kan låsa distribuerade resurser med REST API [för hanteringslås.](/rest/api/resources/managementlocks) Med REST API kan du skapa och ta bort lås och hämta information om befintliga lås.

Om du vill skapa ett lås kör du:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Omfånget kan vara en prenumeration, resursgrupp eller resurs. Låsnamnet är det du vill kalla låset. För API-version använder du **2016-09-01**.

Ta med ett JSON-objekt som anger låsets egenskaper i begäran.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om att organisera dina resurser logiskt finns i Använda [taggar för att organisera dina resurser.](tag-resources.md)
* Du kan tillämpa begränsningar och konventioner i din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
