---
title: 'CLI: Distribuera appfiler med FTP'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service appen. Det här exemplet visar hur du skapar en app och distribuerar filer med FTP.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 875192b5da0c034f4ac92c74dd617ded79df7f45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788015"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Skapa en App Service-app och distribuera filer med FTP och Azure CLI

Det här skriptexemplet skapar en app i App Service med dess relaterade resurser och distribuerar sedan en statisk HTML-sida med FTP. I skriptet används [cURL](https://en.wikipedia.org/wiki/CURL) för FTP-överföring, men detta ska bara ses som ett exempel. Du kan ladda upp dina filer med valfritt FTP-program.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript 

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Skapar en App Service-app. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) | Hämta information om tillgängliga distributionsprofiler för appar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
