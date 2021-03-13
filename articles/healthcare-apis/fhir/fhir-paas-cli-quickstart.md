---
title: 'Snabb start: Distribuera Azure API för FHIR med Azure CLI'
description: I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR i Azure med hjälp av Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10bc0ac3a61ddba22dea46dfdd47a0305e2d9149
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019702"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Snabb start: Distribuera Azure API för FHIR med Azure CLI

I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR i Azure med hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>Lägg till HealthcareAPIs-tillägg

```azurecli-interactive
az extension add --name healthcareapis
```

Hämta en lista med kommandon för HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Skapa Azure-resurs grupp

Välj ett namn för resurs gruppen som ska innehålla Azure API för FHIR och skapa den:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Distribuera Azure API för FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Hämta FHIR API Capability-instruktion

Hämta en funktions sats från FHIR-API: et med:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurs gruppen med följande steg:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure API för FHIR i din prenumeration. Om du vill ange ytterligare inställningar i Azure-API: et för FHIR går du vidare till ytterligare inställningar instruktions guide. Läs mer om hur du registrerar program om du är redo att börja använda Azure API för FHIR.

>[!div class="nextstepaction"]
>[Ytterligare inställningar i Azure API för FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Översikt över program register](fhir-app-registration.md)
