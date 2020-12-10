---
title: Skript exempel för Azure CLI – integrera App Service med Application Gateway | Microsoft Docs
description: Skript exempel för Azure CLI – integrera App Service med Application Gateway
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3820e7bf00f99a846dd2be0edeaf4248e0dfd8ad
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006097"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrera App Service med Application Gateway med CLI

Det här exempel skriptet skapar en Azure App Service-webbapp, en Azure-Virtual Network och en Application Gateway. Den begränsar sedan trafiken för webbappen så att den endast kommer från Application Gateway under nätet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.74 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, App Service-appen, Cosmos DB och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az network vnet create`](/cli/azure/network/vnet#az-network-vnet-create) | Skapar ett virtuellt nätverk. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az-network-public-ip-create) | Skapar en offentlig IP-adress. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az-network-public-ip-show) | Visa information om en offentlig IP-adress. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Skapar ett App Service-webbprogram. |
| [`az webapp show`](/cli/azure/webapp#az-webapp-show) | Visa information om en App Service-webbapp. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az-webapp-config-access-restriction-add) | Lägger till en åtkomst begränsning i App Service-webbappen. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az-network-application-gateway-create) | Skapar en Application Gateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az-network-application-gateway-http-settings-update) | Uppdateringar Application Gateway HTTP-inställningar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
