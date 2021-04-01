---
title: Skapa signal tjänst med App Service med Azure CLI
description: Använd Azure CLI för att skapa signal tjänsten med App Service. Lär dig alla CLI-kommandon för Azure SignalR-tjänsten.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 673a3583b1ec80fb3ad61ec35a1786a59939f6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563332"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Skapa en SignalR Service med en App Service

Det här exempelskriptet skapar en ny Azure SignalR Service-resurs som används för att skicka innehållsuppdateringar i realtid till klienter. Skriptet lägger också till en ny webbapp och App Service-plan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen konfigureras med en appinställning som heter *AzureSignalRConnectionString* för att ansluta till den nya SignalR Service-resursen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet använder tillägget *signalr* för Azure CLI. Utför följande kommando för att installera *signalr*-tillägget för Azure CLI innan du använder det här exempelskriptet:

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

Anteckna det faktiska namnet som genereras för den nya resursgruppen. Det visas i utdata. Du använder resursgruppens namn när du vill ta bort alla gruppresurser.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Skapar en Azure SignalR-tjänstresurs. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Listar nycklarna som kommer att användas av programmet när det skickar uppdateringar av innehåll i realtid med SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en Azure App Service-Plan som värd för webbprogram. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Skapar en Azure-webbapp med hjälp av App Service som värd för planen. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Lägger till en ny appinställning för webbappen. Den här appinställningen används för att lagra SignalR-anslutningssträngen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure SignalR Service CLI-skriptexempel finns i [Azure SignalR Service-dokumentationen](../signalr-reference-cli.md).
