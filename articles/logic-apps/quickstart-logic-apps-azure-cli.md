---
title: Skapa och hantera Logic Apps med Azure CLI
description: Använd Azure CLI för att skapa en Logic app och hantera sedan din Logi Kap par med åtgärder som lista, Visa (Hämta), uppdatera och ta bort.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033382"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Snabb start: skapa och hantera Logic Apps med Azure CLI

Den här snabb starten visar hur du skapar och hanterar Logi Kap par med hjälp av [Azure CLI Logic Apps-tillägget](/cli/azure/ext/logic/logic) ( `az logic` ). Från kommando raden kan du skapa en Logic-app med hjälp av JSON-filen för en logisk app Workflow-definition. Sedan kan du hantera din Logic app genom att köra åtgärder som `list` , `show` ( `get` ), `update` och `delete` från kommando raden.

> [!WARNING]
> Azure CLI Logic Apps-tillägget är för närvarande *experimentellt* och *omfattas inte av kund support*. Använd det här CLI-tillägget med försiktighet, särskilt om du väljer att använda tillägget i produktions miljöer.

Om du inte har använt Logic Apps kan du också lära dig hur du skapar dina första Logi Kap par [via Azure Portal](quickstart-create-first-logic-app-workflow.md), [i Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)och [i Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure CLI](/cli/azure/install-azure-cli) installerat på den lokala datorn.
* [Logic Apps Azure CLI-tillägget](/cli/azure/azure-cli-extensions-list) installerat på datorn. Använd följande kommando för att installera det här tillägget: `az extension add --name logic`
* En [Azure-resurs grupp](#example---create-resource-group) där du kan skapa din Logic app.

### <a name="prerequisite-check"></a>Krav kontroll

Verifiera din miljö innan du börjar:

* Logga in på Azure Portal och kontrol lera att din prenumeration är aktiv genom att köra `az login` .
* Kontrol lera din version av Azure CLI i ett terminalfönster eller kommando fönster genom att köra `az --version` . Den senaste versionen finns i den senaste versions [informationen](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installations guiden för ditt operativ system eller din plattform](/cli/azure/install-azure-cli).

### <a name="example---create-resource-group"></a>Exempel – skapa resurs grupp

Om du inte redan har en resurs grupp för din Logic-app skapar du gruppen med kommandot `az group create` . Till exempel skapar följande kommando en resurs grupp med namnet `testResourceGroup` på platsen `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

Utdata visar `provisioningState` som `Succeeded` när resurs gruppen har skapats:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Arbetsflödesdefinition

Innan du [skapar en ny Logic-app](#create-logic-apps-from-cli) eller [uppdaterar en befintlig Logic-app](#update-logic-apps-from-cli) med hjälp av Azure CLI behöver du en arbets flödes definition för din Logic app. I Azure Portal kan du Visa din Logic Apps-underliggande arbets flödes definition i JSON-format genom att **Växla från designvyn** till **kodvyn**.

När du kör kommandona för att skapa eller uppdatera din Logic-app laddas din arbets flödes definition in som en obligatorisk parameter ( `--definition` ). Du måste skapa en arbets flödes definition som en JSON-fil som följer [språk schemat för arbets flödes definitionen](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Skapa Logic Apps från CLI

Du kan skapa ett Logic app-arbetsflöde från Azure CLI med hjälp av kommandot [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) med en JSON-fil för definitionen.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Kommandot måste innehålla följande [obligatoriska parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters):

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Arbetsflödesdefinition | `--definition` | En JSON-fil med din Logic Apps [arbets flödes definition](#workflow-definition). |
| Location | `--location -l` | Den Azure-region där din Logic app finns. |
| Name | `--name -n` | Namnet på din Logic app. Namnet får bara innehålla bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `()` ) och punkter ( `.` ). Namnet måste också vara unikt i flera regioner. |
| Namn på resursgrupp | `--resource-group -g` | Den [Azure-resurs grupp](../azure-resource-manager/management/overview.md) som du vill skapa din Logic app i. [Skapa en resurs grupp](#example---create-resource-group) innan du börjar om du inte redan har en för din Logic app. |

Du kan också ta med ytterligare [valfria parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) för att konfigurera din Logi Kap par åtkomst kontroller, slut punkter, integrations konto, integrerings tjänst miljö, tillstånd och resurs etiketter.

### <a name="example---create-logic-app"></a>Exempel – skapa Logic app

I det här exemplet skapas ett arbets flöde med namnet `testLogicApp` i resurs gruppen `testResourceGroup` på platsen `westus` . JSON-filen `testDefinition.json` innehåller arbets flödes definitionen.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

När arbets flödet har skapats visar CLI den nya arbets flödes definitionens JSON-kod. Om det inte går att skapa arbets flödet kan du se [listan över möjliga fel](#errors).

## <a name="update-logic-apps-from-cli"></a>Uppdatera Logic Apps från CLI

Du kan också uppdatera ett Logic app-arbetsflöde från Azure CLI med hjälp av kommandot [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) .

Kommandot måste innehålla samma [obligatoriska parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) som när du [skapar en Logic app](#create-logic-apps-from-cli). Du kan också lägga till samma [valfria parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) som när du skapar en Logic app.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Exempel – uppdatera Logic app

I det här exemplet uppdateras [exempel arbets flödet som skapades i föregående avsnitt](#example---create-logic-app) för att använda en annan JSON-definitions fil, `newTestDefinition.json` och lägga till två resurs Taggar `testTag1` och `testTag2` med beskrivnings värden.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

När arbets flödet har uppdaterats visar CLI din Logic Apps uppdaterade arbets flödes definition. Om uppdateringen Miss lyckas, se [listan över möjliga fel](#errors).

## <a name="delete-logic-apps-from-cli"></a>Ta bort Logic Apps från CLI

Du kan ta bort ett Logic app-arbetsflöde från Azure CLI med hjälp av kommandot [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) .

Kommandot måste innehålla följande [obligatoriska parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters):

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Name | `--name -n` | Namnet på din Logic app. |
| Namn på resursgrupp | `-resource-group -g` | Resurs gruppen där din Logic app finns. |

Du kan också ta med en [valfri parameter](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) för att hoppa över bekräftelse meddelanden, `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

CLI: en meddelar sedan dig att bekräfta borttagningen av din Logic app. Du kan hoppa över bekräftelse frågan genom att använda den valfria parametern `--yes -y` med ditt kommando.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Du kan bekräfta borttagningen av en Logi Kap par genom [att lista dina](#list-logic-apps-in-cli)Logi Kap par i CLI eller genom att visa dina Logi Kap par i Azure Portal.

### <a name="example---delete-logic-app"></a>Exempel – ta bort Logic app

I det här exemplet tas det [exempel arbets flöde som skapades i ett föregående avsnitt](#example---create-logic-app) bort.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

När du har besvarat bekräftelse meddelandet med `y` , tas Logic app bort.

## <a name="show-logic-apps-in-cli"></a>Visa Logic Apps i CLI

Du kan hämta ett speciellt Logic app-arbetsflöde med hjälp av kommandot [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

Kommandot måste innehålla följande [obligatoriska parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters)

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Name | `--name -n` | Namnet på din Logic app. |
| Namn på resursgrupp | `--resource-group -g` | Namnet på resurs gruppen där din Logic app finns. |

### <a name="example---get-logic-app"></a>Exempel – hämta Logic app

I det här exemplet returneras den logiska appen `testLogicApp` i resurs gruppen `testResourceGroup` med fullständiga loggar för fel sökning.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Lista Logic Apps i CLI

Du kan lista dina Logi Kap par efter prenumeration med hjälp av kommandot [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) . Det här kommandot returnerar JSON-koden för dina Logic Apps-arbetsflöden.

Du kan filtrera resultaten med följande [valfria parametrar](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters):

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Namn på resursgrupp | `--resource-group -g` | Namnet på den resurs grupp som du vill filtrera resultaten med. |
| Antal objekt | `--top` | Antalet objekt som ingår i resultaten. |
| Filtrera | `--filter` | Den typ av filter som du använder i listan. Du kan filtrera efter tillstånd ( `State` ), utlösare ( `Trigger` ) och identifieraren för den refererade resursen ( `ReferencedResourceId` ). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Exempel på lista Logic Apps

I det här exemplet returneras alla aktiverade arbets flöden i resurs gruppen `testResourceGroup` i ett ASCII-tabell format.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Fel

Följande fel indikerar att Azure Logic Apps CLI-tillägget inte är installerat. Följ stegen i kraven för att [installera Logic Apps tillägget](#prerequisites) på datorn.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Följande fel kan tyda på att fil Sök vägen för att ladda upp arbets flödes definitionen är felaktig.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globala parametrar

Du kan använda följande valfria globala Azure CLI-parametrar med dina `az logic` kommandon:

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Utdataformat | `--output -o` | Ändra [utdataformatet](/cli/azure/format-output-azure-cli) från standard-JSON. |
| Visa endast fel | `--only-show-errors` | Ignorera varningar och Visa bara fel. |
| Verbose | `--verbose` | Visa utförliga loggar. |
| Felsöka | `--debug` | Visar alla fel söknings loggar. |
| Hjälp meddelande | `--help -h` | Visa hjälp dialog ruta. |
| Fråga | `--query` | Ange en JMESPath-frågesträng för JSON-utdata. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure/).

Du hittar ytterligare Logic Apps CLI-skript exempel i [Microsofts webbläsare för kod exempel](/samples/browse/?products=azure-logic-apps).

Sedan kan du skapa ett exempel på en app-logik via Azure CLI med hjälp av ett exempel skript och en arbets flödes definition.

> [!div class="nextstepaction"]
> [Skapa Logic app med exempel skript](sample-logic-apps-cli-script.md).
