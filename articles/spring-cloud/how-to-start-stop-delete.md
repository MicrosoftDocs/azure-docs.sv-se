---
title: Starta, stoppa och ta bort Azure Spring Cloud program | Microsoft Docs
description: Starta, stoppa och ta bort ditt Azure Spring Cloud program
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 914325aba3d123fb1b700f0eff8ddb17119c5d12
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863217"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Starta, stoppa och ta bort ditt Azure Spring Cloud program

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Den här guiden beskriver hur du ändrar ett programtillstånd i Azure Spring Cloud med hjälp av antingen Azure Portal eller Azure CLI.

## <a name="using-the-azure-portal"></a>Använda Azure Portal

När du har distribuerat ett program kan du starta, stoppa och ta bort det med hjälp av Azure Portal.

1. Gå till Azure Spring Cloud-tjänstinstansen i Azure Portal.
1. Välj fliken **Programinstrumentpanel.**
1. Välj det program vars tillstånd du vill ändra.
1. På sidan **Översikt** för programmet väljer du **Starta/stoppa, Starta** **om** eller Ta **bort.**

## <a name="using-the-azure-cli"></a>Använda Azure CLI

> [!NOTE]
> Du kan använda valfria parametrar och konfigurera standardvärden med Azure CLI. Läs mer om Azure CLI i vår [referensdokumentation.](/cli/azure/spring-cloud)  

Installera först Azure Spring Cloud för Azure CLI på följande sätt:

```azurecli
az extension add --name spring-cloud
```

Välj sedan någon av dessa Azure CLI-åtgärder:

* Så här startar du ditt program:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här stoppar du programmet:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här startar du om programmet:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här tar du bort ditt program:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
