---
title: 'CLI: Återställa en app från en säkerhetskopia'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service appen. Det här exemplet visar hur du återställer en app från en säkerhetskopia.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: dd9013e77699deddb680ca1e74c654bac757093f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782575"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Återställa en webbapp från en säkerhetskopia med CLI

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser, och skapar sedan en tidsbegränsad säkerhetskopia för webbappen. 

Om du vill köra skriptet behöver du en befintlig säkerhetskopia för en webbapp. Information om hur du skapar en sådan finns i [Säkerhetskopiera en webbapp](cli-backup-onetime.md) eller [Skapa en schemalagd säkerhetskopiering för en webbapp](cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup#az_webapp_config_backup_restore) | Återställer en webbapp från en säkerhetskopia. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
