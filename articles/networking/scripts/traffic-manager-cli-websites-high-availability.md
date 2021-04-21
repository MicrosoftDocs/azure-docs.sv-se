---
title: Skriptexempel för Azure CLI – Dirigera trafik för hög tillgänglighet för program | Microsoft Docs
description: Skriptexempel för Azure CLI – Dirigera trafik för hög tillgänglighet för program
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 00964a49940b208144167e9fe1ff4248f46e35fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763207"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Dirigera trafik för hög tillgänglighet för program – Azure CLI

Det här skriptet skapar en resursgrupp, två App Service-planer, två webbappar, en Traffic Manager-profil och två Traffic Manager-slutpunkter. Traffic Manager dirigerar trafik till programmet i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen inte är tillgängligt. Innan du kör skriptet måste du ändra värdena för MyWebApp, MyWebAppL1 och MyWebAppL2 till unika värden i Azure. När skriptet har körts kan du komma åt appen i den primära regionen med URL:en mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptexempel har körts kan följande kommando användas för att ta bort resursgruppen, App Service appen och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az appservice plan create](/cli/azure/appservice/plan) | Skapar en App Service-plan. Det här är som en servergrupp för din Azure-webbapp. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | Skapar en Azure-webbapp i App Service plan. |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Skapar en Azure Traffic Manager-profil. |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare App Service CLI-skriptexempel finns i Azure-nätverkstjänster [dokumentationen](../cli-samples.md).