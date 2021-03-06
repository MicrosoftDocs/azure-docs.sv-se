---
title: 'CLI: Skala appen med Traffic Manager'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av App Service app. Det här exemplet visar hur du skalar en global med Traffic Manager.
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 089ab9d6b3c0f678c2a200872a0da4160fa6292a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787679"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Skala en App Service-app globalt med en arkitektur för hög tillgänglighet med hjälp av Azure CLI

Det här exempelskriptet skapar en resursgrupp, två App Service-planer, två appar, en Traffic Manager-profil och två Traffic Manager-slutpunkter. När den här övningen är klar har du en arkitektur med hög tillgänglighet, vilket ger din app global tillgänglighet baserat på den lägsta nätverksfördröjningen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en App Service-app, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Skapar en App Service-app. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Skapar en Azure Traffic Manager-profil. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint#az_network_traffic-manager_endpoint_create) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
