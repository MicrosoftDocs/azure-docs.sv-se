---
title: 'Snabbstart: Skapa en delad fråga med Azure CLI'
description: I den här snabbstarten följer du stegen för att aktivera Resource Graph för Azure CLI och skapa en delad fråga.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 2befc2b6895cd9d2c797fc8ed0e28a27eb6e73e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870561"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Snabbstart: Skapa en Resource Graph delad fråga med Azure CLI

Det första steget för att Azure Resource Graph med [Azure CLI](/cli/azure/) är att kontrollera att tillägget är installerat. Denna snabbstart vägleder dig genom processen för att lägga till tillägget i Azure CLI-installationen. Du kan använda tillägget med Azure CLI installerat lokalt eller via [Azure Cloud Shell](https://shell.azure.com).

I slutet av den här processen har du lagt till tillägget i valfri Azure CLI-installation och skapat en Resource Graph delad fråga.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Lägga till Resource Graph-tillägget

Om du vill aktivera Azure CLI för Azure Resource Graph måste tillägget läggas till. Det här tillägget fungerar överallt där Azure CLI kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (både fristående och i portalen), [Azure CLI-dockeravbildningen](https://hub.docker.com/_/microsoft-azure-cli) och där det är lokalt installerat.

1. Kontrollera att den senaste versionen av Azure CLI är installerad (minst **2.8.0**). Om den ännu inte är installerad följer du [de här instruktionerna](/cli/azure/install-azure-cli-windows).

1. I valfri Azure CLI-miljö använder du [az extension add för](/cli/azure/extension#az_extension_add) att importera Resource Graph med följande kommando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Kontrollera att tillägget har installerats och är den förväntade versionen (minst **1.1.0**) med [az extension list](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Skapa en Resource Graph delad fråga

När Azure CLI-tillägget har lagts till i din miljö är det dags för en Resource Graph delad fråga. Den delade frågan är ett Azure Resource Manager objekt som du kan bevilja behörighet till eller köra i Azure Resource Graph Explorer. Frågan sammanfattar antalet resurser grupperade efter _plats._

1. Skapa en resursgrupp med [az group create för](/cli/azure/group#az_group_create) att lagra Azure Resource Graph delad fråga. Den här resursgruppen heter `resource-graph-queries` och platsen är `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Skapa den Azure Resource Graph delade frågan med hjälp av `graph` tillägget och kommandot az graph [shared-query create:](/cli/azure/graph/shared-query#az_graph_shared_query_create)

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Visa en lista över delade frågor i den nya resursgruppen. Kommandot [az graph shared-query list](/cli/azure/graph/shared-query#az_graph_shared_query_list) returnerar en matris med värden.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Om du bara vill få ett enda delat frågeresultat använder du [kommandot az graph shared-query show.](/cli/azure/graph/shared-query#az_graph_shared_query_show)

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Kör den delade frågan i Azure CLI med `{{shared-query-uri}}` syntaxen i kommandot [az graph query.](/cli/azure/graph#az_graph_query)
   Kopiera först fältet `id` från resultatet från föregående `show` kommando. Ersätt `shared-query-uri` text i exemplet med värdet från `id` fältet, men lämna de omgivande tecknen `{{` och `}}` .

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > Syntaxen `{{shared-query-uri}}` är en **förhandsgranskningsfunktion.**

Ett annat sätt att Resource Graph med delade frågor är via Azure Portal. I portalen använder du sökfältet för att söka efter "Resource Graph frågor". Välj den delade frågan. På sidan **Översikt** visar fliken **Fråga** den sparade frågan. Knappen **Redigera** öppnar den i [Resource Graph Explorer](./first-query-portal.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph delad fråga, resursgrupp och tillägg från Azure CLI-miljön kan du göra det med hjälp av följande kommandon:

- [az graph shared-query delete](/cli/azure/graph/shared-query#az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

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

I den här snabbstarten har du lagt till Resource Graph i Azure CLI-miljön och skapat en delad fråga. Om du vill veta mer Resource Graph språk fortsätter du till sidan med information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)