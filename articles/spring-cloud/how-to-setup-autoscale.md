---
title: Konfigurera autoskalning för mikrotjänstprogram
description: Den här artikeln beskriver hur du konfigurerar inställningar för autoskalning för dina program med hjälp av Microsoft Azure-portalen eller Azure CLI.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: eaa4a219a1020639c930f8a07bfcaa1c45d0cef8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878432"
---
# <a name="set-up-autoscale-for-microservice-applications"></a>Konfigurera autoskalning för mikrotjänstprogram

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Autoskalning är en inbyggd funktion i Azure våren Cloud som hjälper mikrotjänstprogram att utföra sina bästa när efter frågan förändras. Detta inkluderar ändring av antalet virtuella processorer, minne och App-instanser. Den här artikeln beskriver hur du konfigurerar inställningar för autoskalning för dina program med hjälp av Microsoft Azure-portalen eller Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa de här procedurerna behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* En distribuerad Azure våren Cloud Service-instans. Följ [snabb starten när du distribuerar en app via Azure CLI](./spring-cloud-quickstart.md) för att komma igång.
* Minst ett program som redan har skapats i din tjänst instans.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Gå till sidan för autoskalning i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till **översikts** sidan för Azure våren Cloud.
3. Välj den resurs grupp som innehåller din tjänst.
4. Välj fliken **appar** under **Inställningar** i menyn i det vänstra navigerings fönstret.
5. Välj det program som du vill konfigurera autoskalning för. I det här exemplet väljer du programmet **demonstration**. Du bör sedan se programmets **översikts** sida.
6. Gå till fliken **skala ut** under **Inställningar** i menyn i det vänstra navigerings fönstret.
7. Välj den distribution som du vill konfigurera autoskalning. Du bör se alternativ för autoskalning som visas i följande avsnitt.


![Autoskalning-menyn](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Konfigurera inställningar för autoskalning för ditt program i Azure Portal

Det finns två alternativ för att hantera efter frågan på begäran:

* Manuell skalning: upprätthåller ett fast instans antal. På standard-nivån kan du skala ut till högst 500 instanser. Det här värdet ändrar antalet separata instanser av mikrotjänst programmet som körs.
* Anpassad autoskalning: skalar enligt valfritt schema, baserat på eventuella mått.

I Azure Portal väljer du hur du vill skala.  Följande bild visar de anpassade inställningarna för alternativ för **autoskalning** och läge.

![Anpassad autoskalning](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Konfigurera inställningar för autoskalning för ditt program i Azure CLI
Du kan också ange lägen för autoskalning med hjälp av Azure CLI.  Följande kommandon skapar en inställning för autoskalning och en regel för autoskalning.

* Skapa autoskalningsinställning
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Skapa regel för autoskalning
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Uppgradera till standardnivån

Om du är på Basic-nivån och är begränsad av en eller flera av dessa gränser, kan du uppgradera till standard nivån. Det gör du genom att gå till menyn **pris** nivå genom att först välja kolumnen *standard* och klicka på knappen **Uppgradera** .

## <a name="next-steps"></a>Nästa steg

* [Översikt över autoskalning i Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md)
* [Autoskalning av Azure CLI-övervakning](/cli/azure/monitor/autoscale)
