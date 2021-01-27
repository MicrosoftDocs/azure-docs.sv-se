---
title: 'Snabb start: din första Azure CLI-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs diagram tillägget för Azure CLI och kör din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5111f59eb760afda4e206837ca5bdf8bcc201338
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917849"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Snabb start: kör din första resurs diagram fråga med Azure CLI

Det första steget till att använda Azure Resource Graph är att kontrollera att tillägget för [Azure CLI](/cli/azure/) är installerat. Denna snabbstart vägleder dig genom processen för att lägga till tillägget i Azure CLI-installationen. Du kan använda tillägget med Azure CLI installerat lokalt eller via [Azure Cloud Shell](https://shell.azure.com).

I slutet av den här processen kommer du att ha lagt till tillägget till valfri Azure CLI-installation och kört din första Resource Graph-fråga.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Lägga till Resource Graph-tillägget

Om du vill aktivera Azure CLI för att skicka frågor till Azure Resource Graph, måste du lägga till tillägget. Det här tillägget fungerar överallt där Azure CLI kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (både fristående och i portalen), [Azure CLI-dockeravbildningen](https://hub.docker.com/_/microsoft-azure-cli) och där det är lokalt installerat.

1. Kontrol lera att den senaste versionen av Azure CLI är installerad (minst **2.0.76**). Om den ännu inte är installerad följer du [de här instruktionerna](/cli/azure/install-azure-cli-windows).

1. I din valda Azure CLI-miljö importerar du den med följande kommando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Kontrol lera att tillägget har installerats och att den är den förväntade versionen (minst **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Nu när Azure CLI-tillägget har lagts till i din valda miljö är det dags att testa en enkel Resource Graph-fråga. Frågan returnerar de första fem Azure-resurserna med **namn** och **resurstyp** för varje resurs.

1. Kör din första Azure Resource Graph-fråga med hjälp av tillägget `graph` och kommandot `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera frågan till `order by` egenskapen **namn**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Uppdatera frågan till att först `order by`**Namn**-egenskapen och sedan sätta en `limit` för de fem främsta resultaten:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph-tillägget från din Azure CLI-miljö, kan göra du det med hjälp av följande kommando:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lagt till resurs diagram tillägget i din Azure CLI-miljö och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
