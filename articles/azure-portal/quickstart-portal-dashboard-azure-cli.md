---
title: Skapa en Azure Portal instrumentpanel med Azure CLI
description: 'Snabbstart: Lär dig hur du skapar en instrumentpanel i Azure Portal med hjälp av Azure CLI. En instrumentpanel är en fokuserad och ordnad vy över dina molnresurser.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: 581c8cc4c2da275467bc39c5c2008b29a5bc0e0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875763"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Snabbstart: Skapa en Azure Portal instrumentpanel med Azure CLI

En instrumentpanel i Azure Portal är en fokuserad och ordnad vy över dina molnresurser. Den här artikeln fokuserar på processen att använda Azure CLI för att skapa en instrumentpanel.
Instrumentpanelen visar prestanda för en virtuell dator (VM), samt viss statisk information och länkar.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Om du har flera Azure-prenumerationer väljer du lämplig prenumeration som du vill fakturera resurserna i.
Välj en prenumeration med kommandot [az account set:](/cli/azure/account#az_account_set)

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [az group create](/cli/azure/group#az_group_create) eller använd en befintlig resursgrupp:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm](/cli/azure/vm#az_vm_create) create:

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Lösenordet måste vara komplext.
> Det här är ett nytt användarnamn och lösenord.
> Det är till exempel inte det konto som du använder för att logga in på Azure.
> Mer information finns i krav [för användarnamn och](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) [lösenord.](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

Distributionen startar och tar vanligtvis några minuter att slutföra.
När distributionen är klar går du vidare till nästa avsnitt.

## <a name="download-the-dashboard-template"></a>Ladda ned instrumentpanelsmallen

Eftersom Azure-instrumentpaneler är resurser kan de representeras som JSON.
Mer information finns i [Strukturen för Azure-instrumentpaneler.](./azure-portal-dashboards-structure.md)

Ladda ned följande fil: [portal-dashboard-template-testvm.jspå](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Anpassa den nedladdade mallen genom att ändra följande värden till dina värden:

* `<subscriptionID>`: Din prenumeration
* `<rgName>`: Resursgrupp, till exempel `myResourceGroup`
* `<vmName>`: Namn på virtuell dator, till exempel `SimpleWinVM`
* `<dashboardTitle>`: Instrumentpanelsrubrik, till exempel `Simple VM Dashboard`
* `<location>`: Din Azure-region, till exempel `centralus`

Mer information finns i [mallreferensen för instrumentpaneler i Microsoft Portal.](/azure/templates/microsoft.portal/dashboards)

## <a name="deploy-the-dashboard-template"></a>Distribuera instrumentpanelens mall

Nu kan du distribuera mallen från Azure CLI.

1. Kör kommandot [az portal dashboard create](/cli/azure/portal/dashboard#az_portal_dashboard_create) för att distribuera mallen:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Kontrollera att instrumentpanelen har skapats genom att köra kommandot [az portal dashboard show:](/cli/azure/portal/dashboard#az_portal_dashboard_show)

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Om du vill se alla instrumentpaneler för den aktuella prenumerationen använder du [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Du kan också se alla instrumentpaneler för en resursgrupp:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Du kan uppdatera en instrumentpanel med hjälp av [kommandot az portal dashboard update:](/cli/azure/portal/dashboard#az_portal_dashboard_update)

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den virtuella datorn och den associerade instrumentpanelen tar du bort resursgruppen som innehåller dem.

> [!CAUTION]
> I följande exempel tas den angivna resursgruppen och alla resurser som ingår i den bort.
> Om det finns resurser utanför omfånget för den här artikeln i den angivna resursgruppen tas de också bort.

```azurecli
az group delete --name myResourceGroup
```

Om du bara vill ta bort instrumentpanelen använder du [kommandot az portal dashboard delete:](/cli/azure/portal/dashboard#az_portal_dashboard_delete)

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI-stöd för instrumentpaneler finns i [az portal dashboard](/cli/azure/portal/dashboard).
