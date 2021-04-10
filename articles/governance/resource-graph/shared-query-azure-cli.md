---
title: 'Snabb start: skapa en delad fråga med Azure CLI'
description: I den här snabb starten följer du stegen för att aktivera resurs diagram tillägget för Azure CLI och skapa en delad fråga.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ec1b010771790339a13777624f04c7bd2db01f11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594391"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Snabb start: skapa en resurs diagram delad fråga med Azure CLI

Det första steget i att använda Azure Resource Graph med [Azure CLI](/cli/azure/) är att kontrol lera att tillägget är installerat. Denna snabbstart vägleder dig genom processen för att lägga till tillägget i Azure CLI-installationen. Du kan använda tillägget med Azure CLI installerat lokalt eller via [Azure Cloud Shell](https://shell.azure.com).

I slutet av den här processen har du lagt till tillägget till din Azure CLI-installation och skapa en delad fråga för resurs diagram.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Lägga till Resource Graph-tillägget

Om du vill att Azure CLI ska fungera med Azure Resource Graph måste tillägget läggas till. Det här tillägget fungerar överallt där Azure CLI kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (både fristående och i portalen), [Azure CLI-dockeravbildningen](https://hub.docker.com/_/microsoft-azure-cli) och där det är lokalt installerat.

1. Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.8.0**). Om den ännu inte är installerad följer du [de här instruktionerna](/cli/azure/install-azure-cli-windows).

1. I den Azure CLI-miljö du väljer använder du [tillägget AZ Add](/cli/azure/extension#az_extension_add) för att importera resurs diagram tillägget med följande kommando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Kontrol lera att tillägget har installerats och att den förväntade versionen (minst **1.1.0**) med AZ- [filtillägg visas](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Skapa en delad resurs diagrams fråga

När Azure CLI-tillägget har lagts till i din miljö väljer du en delad fråga för resurs diagram. Den delade frågan är ett Azure Resource Manager objekt som du kan ge behörighet till eller köra i Azure Resource Graph Explorer. Frågan sammanfattar antalet resurser grupperade efter _plats_.

1. Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az_group_create) för att lagra den delade frågan i Azure Resource Graph. Den här resurs gruppen har namnet `resource-graph-queries` och platsen är `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Skapa en delad Azure Resource Graph-fråga med hjälp av `graph` tillägget och [AZ graf Shared Query Create](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_create) , kommando:

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Visa en lista över delade frågor i den nya resurs gruppen. Kommandot [AZ graf Shared-Query List](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_list) returnerar en matris med värden.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Om du bara vill ha ett enda delat frågeresultat använder du kommandot [AZ Graph Shared Query show](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_show) .

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Kör den delade frågan i Azure CLI med `{{shared-query-uri}}` syntaxen i ett [AZ Graph Query](/cli/azure/ext/resource-graph/graph#ext_resource_graph_az_graph_query) -kommando.
   Kopiera först `id` fältet från resultatet från föregående `show` kommando. Ersätt `shared-query-uri` text i exemplet med värdet från `id` fältet, men lämna omgivande `{{` `}}` tecken och tecken.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}`Syntaxen är en **förhands gransknings** funktion.

Ett annat sätt att hitta delade frågor för resurs diagram är genom Azure Portal. I portalen använder du Sök fältet för att söka efter "resurs diagram frågor". Välj den delade frågan. På sidan **Översikt** visar fliken **fråga** den sparade frågan. Knappen **Redigera** öppnas i [resurs diagram Utforskaren](./first-query-portal.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den delade frågan, resurs gruppen och tillägget för resurs grafen från Azure CLI-miljön kan du göra det med hjälp av följande kommandon:

- [AZ graf Shared-Query Delete](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [ta bort AZ-tillägg](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lagt till resurs diagram tillägget i din Azure CLI-miljö och skapat en delad fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)