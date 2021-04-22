---
title: Skapa och hantera logikappar med Azure CLI
description: Använd Azure CLI för att skapa en logikapp och hantera sedan logikappen med åtgärder som list, visa (hämta), uppdatera och ta bort.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: afc39673a30f5c99455696c7a075cb1a6a33ecd1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875511"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Snabbstart: Skapa och hantera logikappar med hjälp av Azure CLI

Den här snabbstarten visar hur du skapar och hanterar logikappar med hjälp av [Azure CLI Logic Apps tillägget](/cli/azure/logic) ( `az logic` ). Från kommandoraden kan du skapa en logikapp med hjälp av JSON-filen för en arbetsflödesdefinition för logikappen. Du kan sedan hantera logikappen genom att köra åtgärder `list` som , ( ), och från `show` `get` `update` `delete` kommandoraden.

> [!WARNING]
> Azure CLI-Logic Apps-tillägget är för närvarande *experimentellt* *och omfattas inte av kundsupporten.* Använd det här CLI-tillägget med försiktighet, särskilt om du väljer att använda tillägget i produktionsmiljöer.

Om du inte har Logic Apps kan du också lära dig hur du skapar dina första logikappar via [Azure Portal](quickstart-create-first-logic-app-workflow.md), [i Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)och i [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Azure CLI installerat](/cli/azure/install-azure-cli) på den lokala datorn.
* Den [Logic Apps Azure CLI-tillägget](/cli/azure/azure-cli-extensions-list) som är installerat på datorn. Använd det här kommandot för att installera det här tillägget: `az extension add --name logic`
* En [Azure-resursgrupp](#example---create-resource-group) där du kan skapa logikappen.

### <a name="prerequisite-check"></a>Kravkontroll

Verifiera din miljö innan du börjar:

* Logga in på Azure Portal och kontrollera att din prenumeration är aktiv genom att köra `az login` .
* Kontrollera din version av Azure CLI i en terminal eller ett kommandofönster genom att köra `az --version` . Den senaste versionen finns i den senaste [versionsanteckningarna.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installationsguiden för ditt operativsystem eller din plattform.](/cli/azure/install-azure-cli)

### <a name="example---create-resource-group"></a>Exempel – skapa resursgrupp

Om du inte redan har en resursgrupp för logikappen skapar du gruppen med kommandot `az group create` . Följande kommando skapar till exempel en resursgrupp med `testResourceGroup` namnet på platsen `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

Utdata visar `provisioningState` som `Succeeded` när resursgruppen har skapats:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Arbetsflödesdefinition

Innan du [skapar en ny logikapp eller](#create-logic-apps-from-cli) uppdaterar en befintlig [logikapp](#update-logic-apps-from-cli) med hjälp av Azure CLI behöver du en arbetsflödesdefinition för logikappen. I Azure Portal kan du visa logikappens underliggande arbetsflödesdefinition i JSON-format genom att växla från **designervyn** till **kodvyn**.

När du kör kommandona för att skapa eller uppdatera logikappen laddas arbetsflödesdefinitionen upp som en obligatorisk parameter ( `--definition` ). Du måste skapa arbetsflödesdefinitionen som en JSON-fil som följer [schemat arbetsflödesdefinitionsspråk.](./logic-apps-workflow-definition-language.md)

## <a name="create-logic-apps-from-cli"></a>Skapa logikappar från CLI

Du kan skapa ett logikapparbetsflöde från Azure CLI med hjälp av [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) kommandot med en JSON-fil för definitionen.

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

Kommandot måste innehålla följande [obligatoriska parametrar:](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters)

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Arbetsflödesdefinition | `--definition` | En JSON-fil med logikappens [arbetsflödesdefinition](#workflow-definition). |
| Location | `--location -l` | Den Azure-region där logikappen finns. |
| Name | `--name -n` | Namnet på logikappen. Namnet får bara innehålla bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ), parenteser ( `()` ) och punkter ( `.` ). Namnet måste också vara unikt i olika regioner. |
| Namn på resursgrupp | `--resource-group -g` | Den [Azure-resursgrupp](../azure-resource-manager/management/overview.md) där du vill skapa logikappen. [Skapa en resursgrupp](#example---create-resource-group) innan du börjar om du inte redan har en för logikappen. |

Du kan också inkludera ytterligare [valfria](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) parametrar för att konfigurera logikappens åtkomstkontroller, slutpunkter, integrationskonto, integrationstjänstmiljö, tillstånd och resurstaggar.

### <a name="example---create-logic-app"></a>Exempel – skapa logikapp

I det här exemplet skapas ett `testLogicApp` arbetsflöde med namnet i `testResourceGroup` resursgruppen på platsen `westus` . JSON-filen `testDefinition.json` innehåller arbetsflödesdefinitionen.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

När arbetsflödet har skapats visar CLI JSON-koden för den nya arbetsflödesdefinitionen. Om det inte går att skapa arbetsflödet kan [du se listan över möjliga fel.](#errors)

## <a name="update-logic-apps-from-cli"></a>Uppdatera logikappar från CLI

Du kan också uppdatera logikappens arbetsflöde från Azure CLI med hjälp av kommandot [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) .

Kommandot måste innehålla samma obligatoriska [parametrar som](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters) när du skapar [en logikapp](#create-logic-apps-from-cli). Du kan också lägga till samma [valfria parametrar som](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) när du skapar en logikapp.

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

### <a name="example---update-logic-app"></a>Exempel – uppdatera logikapp

I det här exemplet [uppdateras](#example---create-logic-app) exempelarbetsflödet som skapades i föregående avsnitt för att använda en annan JSON-definitionsfil, , och lägga till två resurstaggar och `newTestDefinition.json` med `testTag1` `testTag2` beskrivningsvärden.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

När arbetsflödet har uppdaterats visar CLI logikappens uppdaterade arbetsflödesdefinition. Om uppdateringen misslyckas kan du se [listan över möjliga fel.](#errors)

## <a name="delete-logic-apps-from-cli"></a>Ta bort logikappar från CLI

Du kan ta bort en logikapps arbetsflöde från Azure CLI med hjälp av kommandot [`az logic workflow delete`](/cli/azure/logic/workflow#az_logic_workflow_delete) .

Kommandot måste innehålla följande [obligatoriska parametrar:](/cli/azure/logic/workflow#az_logic_workflow_delete-required-parameters)

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Name | `--name -n` | Namnet på logikappen. |
| Namn på resursgrupp | `-resource-group -g` | Den resursgrupp där logikappen finns. |

Du kan också ta med en [valfri parameter för](/cli/azure/logic/workflow#az_logic_workflow_delete-optional-parameters) att hoppa över bekräftelseuppfrågarna, `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

CLI uppmanar dig sedan att bekräfta borttagningen av logikappen. Du kan hoppa över bekräftelsemeddelandet med hjälp av den valfria `--yes -y` parametern med kommandot .

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Du kan bekräfta borttagningen av en logikapp genom att visa dina logikappar i [CLI](#list-logic-apps-in-cli)eller genom att visa logikapparna i Azure Portal.

### <a name="example---delete-logic-app"></a>Exempel – ta bort logikapp

I det här exemplet tas [exempelarbetsflödet som skapades i ett tidigare](#example---create-logic-app) avsnitt bort.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

När du har svarat på bekräftelsemeddelandet `y` med tas logikappen bort.

## <a name="show-logic-apps-in-cli"></a>Visa logikappar i CLI

Du kan hämta ett specifikt logikapparbetsflöde med hjälp av kommandot [`az logic workflow show`](/cli/azure/logic/workflow#az_logic_workflow_show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

Kommandot måste innehålla följande [obligatoriska parametrar](/cli/azure/logic/workflow#az_logic_workflow_show-required-parameters)

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Name | `--name -n` | Namnet på logikappen. |
| Namn på resursgrupp | `--resource-group -g` | Namnet på resursgruppen där logikappen finns. |

### <a name="example---get-logic-app"></a>Exempel – hämta logikapp

I det här exemplet returneras `testLogicApp` logikappen i `testResourceGroup` resursgruppen med fullständiga loggar för felsökning.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Lista logikappar i CLI

Du kan lista dina logikappar efter prenumeration med hjälp av kommandot [`az logic workflow list`](/cli/azure/logic/workflow#az_logic_workflow_list) . Det här kommandot returnerar JSON-koden för logikappens arbetsflöden.

Du kan filtrera resultaten efter följande [valfria parametrar:](/cli/azure/logic/workflow#az_logic_workflow_list-optional-parameters)

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Namn på resursgrupp | `--resource-group -g` | Namnet på den resursgrupp som du vill filtrera resultaten efter. |
| Antal objekt | `--top` | Antalet objekt som ingår i dina resultat. |
| Filtrera | `--filter` | Den typ av filter som du använder i listan. Du kan filtrera efter tillstånd ( `State` ), utlösare ( `Trigger` ) och identifieraren för den refererade resursen ( `ReferencedResourceId` ). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Exempel – lista logikappar

I det här exemplet returneras alla aktiverade arbetsflöden i `testResourceGroup` resursgruppen i ett ASCII-tabellformat.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Fel

Följande fel anger att Azure Logic Apps CLI-tillägget inte är installerat. Följ stegen i förutsättningarna för att [installera Logic Apps på](#prerequisites) datorn.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Följande fel kan tyda på att filsökvägen för att ladda upp arbetsflödesdefinitionen är felaktig.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globala parametrar

Du kan använda följande valfria globala Azure CLI-parametrar med dina `az logic` kommandon:

| Parameter | Värde | Beskrivning |
| --------- | ----- | ----------- |
| Utdataformat | `--output -o` | Ändra [utdataformatet](/cli/azure/format-output-azure-cli) från JSON-standardformatet. |
| Visa endast fel | `--only-show-errors` | Ignorera varningar och visa endast fel. |
| Verbose | `--verbose` | Visa utförliga loggar. |
| Felsöka | `--debug` | Visar alla felsökningsloggar. |
| Hjälpmeddelande | `--help -h` | Visa hjälpdialogruta. |
| Fråga | `--query` | Ange en JMESPath-frågesträng för JSON-utdata. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen.](/cli/azure/)

Du hittar ytterligare Logic Apps CLI-skriptexempel [i Microsofts webbläsare för kodexempel.](/samples/browse/?products=azure-logic-apps)

Därefter kan du skapa en exempelapplogik via Azure CLI med hjälp av ett exempelskript och en arbetsflödesdefinition.

> [!div class="nextstepaction"]
> [Skapa en logikapp med exempelskriptet](sample-logic-apps-cli-script.md).
