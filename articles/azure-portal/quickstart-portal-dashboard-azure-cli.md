---
title: Skapa en Azure Portal instrument panel med Azure CLI
description: 'Snabb start: Lär dig hur du skapar en instrument panel i Azure Portal med hjälp av Azure CLI. En instrument panel är en fokuserad och ordnad vy av dina moln resurser.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ddfee1932c6887c6ca7593ca7a28c03e68e09899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104613251"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Snabb start: skapa en Azure Portal instrument panel med Azure CLI

En instrument panel i Azure Portal är en fokuserad och ordnad vy av dina moln resurser. Den här artikeln fokuserar på hur du använder Azure CLI för att skapa en instrument panel.
Instrument panelen visar prestanda för en virtuell dator (VM) samt en statisk information och länkar.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Om du har flera Azure-prenumerationer väljer du den prenumeration som du vill debitera resurserna i.
Välj en prenumeration med kommandot [AZ Account set](/cli/azure/account#az_account_set) :

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med kommandot [AZ Group Create](/cli/azure/group#az_group_create) eller Använd en befintlig resurs grupp:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [AZ VM Create](/cli/azure/vm#az_vm_create) :

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Lösen ordet måste vara komplext.
> Detta är ett nytt användar namn och lösen ord.
> Det är till exempel inte det konto som du använder för att logga in på Azure.
> Mer information finns i [användar namn krav](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) och [lösen ords krav](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Distributionen startar och tar vanligt vis några minuter att slutföra.
När distributionen är klar går du vidare till nästa avsnitt.

## <a name="download-the-dashboard-template"></a>Hämta instrument panelens mall

Eftersom Azure-instrumentpaneler är resurser kan de representeras som JSON.
Mer information finns i [strukturen för Azure-instrumentpaneler](./azure-portal-dashboards-structure.md).

Ladda ned följande fil: [portal-dashboard-template-testvm.jspå](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Anpassa den nedladdade mallen genom att ändra följande värden till dina värden:

* `<subscriptionID>`: Din prenumeration
* `<rgName>`: Resurs grupp, till exempel `myResourceGroup`
* `<vmName>`: Namn på virtuell dator, till exempel `SimpleWinVM`
* `<dashboardTitle>`: Instrument panels rubrik, till exempel `Simple VM Dashboard`
* `<location>`: Din Azure-region, till exempel `centralus`

Mer information finns i [referens för Microsoft Portal-instrumentpaneler](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Distribuera instrument panelens mall

Nu kan du distribuera mallen inifrån Azure CLI.

1. Kör kommandot [AZ Portal Dashboard Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) för att distribuera mallen:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Kontrol lera att instrument panelen har skapats genom att köra kommandot [AZ Portal Dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) :

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Om du vill se alla instrument paneler för den aktuella prenumerationen använder du [AZ Portal Dashboard List](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Du kan också se alla instrument paneler för en resurs grupp:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Du kan uppdatera en instrument panel med hjälp av [AZ-portalens instrument panels uppdaterings](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) kommando:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort den virtuella datorn och den associerade instrument panelen genom att ta bort resurs gruppen som innehåller dem.

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här artikeln finns i den angivna resurs gruppen kommer de också att tas bort.

```azurecli
az group delete --name myResourceGroup
```

Om du bara vill ta bort instrument panelen använder du kommandot [AZ Portal Dashboard Delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) :

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI-stöd för instrument paneler finns i [instrument panelen för AZ-portalen](/cli/azure/ext/portal/portal/dashboard).
