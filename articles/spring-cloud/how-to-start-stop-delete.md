---
title: Starta, stoppa och ta bort ditt Azure våren Cloud-program | Microsoft Docs
description: Starta, stoppa och ta bort ditt Azure våren Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 46d79de612a17c7b21e7cc51dcf4904209e1625d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878281"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Starta, stoppa och ta bort ditt Azure våren Cloud-program

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

I den här guiden beskrivs hur du ändrar ett programs tillstånd i Azure våren-molnet med hjälp av antingen Azure Portal eller Azure CLI.

## <a name="using-the-azure-portal"></a>Använda Azure Portal

När du har distribuerat ett program kan du starta, stoppa och ta bort det med hjälp av Azure Portal.

1. Gå till din Azure våren Cloud Service-instans i Azure Portal.
1. Välj fliken **instrument panel för program** .
1. Välj det program vars tillstånd du vill ändra.
1. På sidan **Översikt** för programmet väljer du **Starta/stoppa**, **starta om** eller **ta bort**.

## <a name="using-the-azure-cli"></a>Använda Azure CLI

> [!NOTE]
> Du kan använda valfria parametrar och konfigurera standardvärden med Azure CLI. Läs mer om Azure CLI genom att läsa [vår referens dokumentation](/cli/azure/ext/spring-cloud/spring-cloud).  

Installera först moln tillägget Azure våren för Azure CLI enligt följande:

```azurecli
az extension add --name spring-cloud
```

Välj sedan någon av följande Azure CLI-åtgärder:

* Starta programmet:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här stoppar du ditt program:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Starta om programmet:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ta bort ditt program:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
