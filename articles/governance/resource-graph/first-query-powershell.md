---
title: 'Snabbstart: Din första PowerShell-fråga'
description: I den här snabbstarten följer du stegen för att aktivera Resource Graph-modulen för Azure PowerShell och köra din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: e5e276e3be80354eeaaeba2821eb9e3242b368ad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533071"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Snabbstart: Kör din första Resource Graph fråga med Azure PowerShell

Det första steget till att använda Azure Resource Graph är att kontrollera att modulen för Azure PowerShell är installerad. Denna snabbstart vägleder dig genom processen för att lägga till modulen i Azure PowerShell-installationen.

I slutet av den här processen kommer du att ha lagt till modulen till valfri Azure PowerShell-installation och kört din första Resource Graph-fråga.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Lägga till Resource Graph-modulen

Om du vill aktivera Azure PowerShell för att skicka frågor till Azure Resource Graph, måste du lägga till modulen. Den här modulen kan användas med lokalt installerad PowerShell, [med Azure Cloud Shell](https://shell.azure.com), eller med [PowerShell Docker-avbildningen](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Grundläggande krav

Azure Resource Graph-modulen måste ha följande programvara:

- Azure PowerShell 1.0.0 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 eller högre. Om den inte är installerad eller uppdaterad följer du [de här instruktionerna](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Installera modulen

Resource Graph-modulen för PowerShell är **Az.ResourceGraph**.

1. Från en **administrativ** PowerShell-prompt kör du följande kommando:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Kontrollera att modulen har importerats och är den senaste versionen (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Nu när Azure PowerShell-modulen har lagts till i din valda miljö är det dags att testa en enkel Resource Graph-fråga. Frågan returnerar de första fem Azure-resurserna **med namn** och **resurstyp** för varje resurs.

1. Kör din första Azure Resource Graph-fråga med hjälp av cmdlet:et `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera frågan till `order by` egenskapen **namn**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommandoordningen begränsar först frågeresultatet och beställer dem sedan.

1. Uppdatera frågan till att först `order by`**Namn**-egenskapen och sedan sätta en `limit` för de fem främsta resultaten:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

När den sista frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultaten som returneras konsekventa och sorterade efter egenskapen **Namn,** men fortfarande begränsade till de fem främsta resultaten.

> [!NOTE]
> Om frågan inte returnerar resultat från en prenumeration som du redan har åtkomst till kan du observera att cmdleten som standard är `Search-AzGraph` prenumerationer i standardkontexten. Om du vill se en lista över prenumerations-ID:n som ingår i standardkontexten kör du det här Om du vill söka efter alla prenumerationer som du har åtkomst till kan du ange `(Get-AzContext).Account.ExtendedProperties.Subscriptions` PSDefaultParameterValues för cmdlet genom att köra `Search-AzGraph``$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph-modulen från din Azure PowerShell-miljö, kan du göra det med hjälp av följande kommando:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Modulfilen som laddades ned tidigare tas inte bort. Det tar bara bort den från den körda PowerShell-sessionen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lagt till modulen Resource Graph i din Azure PowerShell miljö och kört din första fråga. Om du vill veta mer Resource Graph språk fortsätter du till sidan med information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
