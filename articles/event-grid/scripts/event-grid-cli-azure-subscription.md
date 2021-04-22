---
title: Skriptexempel för Azure CLI – Prenumerera på en Azure-prenumeration| Microsoft Docs
description: Den här artikeln innehåller ett Azure CLI-exempelskript som visar hur du prenumererar på Azure Event Grid händelser med Azure CLI.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7456f9c0da4963c1d3772169faf4963b8af6562d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871431"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Prenumerera på händelser för en Azure-prenumeration med Azure CLI

Det här skriptet skapar en Event Grid-prenumeration på händelser för en Azure-prenumeration.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-tillägget. Installera genom att köra `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Exempelskript – tillägg för förhandsversion

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Skapa en Event Grid-prenumeration. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) – version av tillägg | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* Information om att köra frågor mot prenumerationer finns i [Query Event Grid subscriptions](../query-event-subscriptions.md) (Köra frågor mot Event Grid-prenumerationer).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
