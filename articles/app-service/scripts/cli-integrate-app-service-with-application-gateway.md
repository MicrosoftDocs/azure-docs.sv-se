---
title: Skriptexempel för Azure CLI – Integrera App Service med Application Gateway | Microsoft Docs
description: Skriptexempel för Azure CLI – Integrera App Service med Application Gateway
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
ms.openlocfilehash: d30cc27fc3c546619e85bb9aabd0b31c10102e96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787817"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrera App Service med Application Gateway cli

Det här exempelskriptet skapar Azure App Service en webbapp, en Azure Virtual Network och en Application Gateway. Den begränsar sedan webbappens trafik så att den endast kommer från Application Gateway undernätet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0.74 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, App Service-appen, Cosmos DB och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az network vnet create`](/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt nätverk. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az_network_public_ip_show) | Visa information om en offentlig IP-adress. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Skapar en App Service-webbapp. |
| [`az webapp show`](/cli/azure/webapp#az_webapp_show) | Visa information om en App Service webbapp. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | Lägger till en åtkomstbegränsning till App Service webbappen. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az_network_application_gateway_create) | Skapar en Application Gateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az_network-application-gateway-http_settings_update) | Uppdateringar Application Gateway HTTP-inställningar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
