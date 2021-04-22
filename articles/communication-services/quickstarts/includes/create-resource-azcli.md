---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 69857915620eada94586754a6c934edaf0b294a9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879579"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/dotnet/)
- Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-resource"></a>Skapa en Azure-kommunikationsresurs

Om du vill Azure Communication Services en resurs [loggar du in på Azure CLI](/cli/azure/authenticate-azure-cli). Du kan göra detta via terminalen med kommandot ```az login``` och ange dina autentiseringsuppgifter. Kör följande kommando för att skapa resursen:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Om du vill välja en specifik prenumeration kan du också ange flaggan ```--subscription``` och prenumerations-ID:t.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

Du kan konfigurera Communication Services resurs med följande alternativ:

* Resursgruppen
* Namnet på Communication Services resursen
* Det geografiska område som resursen kommer att associeras med

I nästa steg kan du tilldela taggar till resursen. Taggar kan användas för att organisera dina Azure-resurser. Mer information [om taggar finns i](../../../azure-resource-manager/management/tag-resources.md) dokumentationen om resurstaggar.

## <a name="manage-your-communication-services-resource"></a>Hantera din Communication Services resurs

Om du vill lägga till taggar Communication Services resursresursen kör du följande kommandon. Du kan även rikta in dig på en specifik prenumeration.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Information om ytterligare kommandon finns i [az communication](/cli/azure/communication).
