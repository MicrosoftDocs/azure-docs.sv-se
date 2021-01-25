---
title: 'CLI: säkerhetskopiera en app'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service-app. Det här exemplet visar hur du säkerhetskopierar en app.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 4f87e610c1f3315b21a9c157f127e713ff5444f2
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747549"
---
# <a name="back-up-an-app-using-cli"></a>Säkerhetskopiera en app med CLI

Det här exempelskriptet skapar en app i App Service med dess relaterade resurser och skapar därefter en tidsbegränsad säkerhetskopia för den. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az storage account create`](/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto. |
| [`az storage container create`](/cli/azure/storage/container#az-storage-container-create) | Skapar en Azure-lagringscontainer. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az-storage-container-generate-sas) | Genererar en SAS-token för en Azure-lagringscontainer.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Skapar en App Service-app. |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup#az-webapp-config-backup-create) | Skapar en säkerhetskopia för en App Service-app. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | Hämtar en lista med säkerhetskopior för en App Service-app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
