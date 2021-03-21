---
title: Snabb start – etablera moln tjänsten Azure våren
description: Beskriver hur du skapar en Azure våren Cloud-tjänstinstans för att distribuera appar.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6f25c4172b384abd487d2084f31981d16e73ee93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209891"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Snabb start: etablera Azure våren Cloud service

::: zone pivot="programming-language-csharp"
I den här snabb starten använder du Azure CLI för att etablera en instans av moln tjänsten Azure våren.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Azure våren Cloud Service stöder .NET Core 3,1 och senare versioner.
* [Azure CLI-versionen 2.0.67 eller högre](/cli/azure/install-azure-cli).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Installera Azure CLI-tillägg

Kontrol lera att din Azure CLI-version är 2.0.67 eller senare:

```azurecli
az --version
```

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Logga in på Azure

1. Logga in på Azure CLI.

    ```azurecli
    az login
    ```

1. Om du har mer än en prenumeration väljer du den som du vill använda för den här snabb starten.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Etablera en instans av Azure våren Cloud

1. Skapa en [resurs grupp](../azure-resource-manager/management/overview.md) som innehåller din Azure våren Cloud-tjänst. Resurs gruppens namn får innehålla alfanumeriska tecken, under streck, parenteser, bindestreck, punkt (förutom i slutet) och Unicode-tecken.

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Etablera en instans av moln tjänsten Azure våren. Tjänst instans namnet måste vara unikt, vara mellan 4 och 32 tecken långt och får bara innehålla gemener, siffror och bindestreck. Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Det kan ta flera minuter att slutföra kommandot.

1. Ange standard resurs grupps namnet och tjänst instans namnet så att du inte behöver ange dessa värden flera gånger i efterföljande kommandon.

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
Du kan skapa ett Azure våren-moln med hjälp av Azure Portal eller Azure CLI.  Båda metoderna beskrivs i följande procedurer.
## <a name="prerequisites"></a>Förutsättningar

* [Installera JDK 8](/java/azure/jdk/)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](/cli/azure/install-azure-cli) och installera Azure våren Cloud-tillägget med kommandot: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [inloggning](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Etablera en instans av Azure våren Cloud

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Följande procedur skapar en instans av Azure våren Cloud med hjälp av Azure Portal.

1. Öppna [Azure Portal](https://ms.portal.azure.com/)på en ny flik. 

2. Sök efter **Azure våren Cloud** i den översta sökrutan.

3. Välj **Azure våren Cloud** från resultaten.

    ![ASC-ikon start](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. På sidan Azure våren Cloud klickar du på **+ Lägg till**.

    ![ASC-ikon Lägg till](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Fyll i formuläret på sidan Azure våren Cloud **create** .  Läs igenom följande riktlinjer:
    - **Prenumeration**: Välj den prenumeration som du vill fakturera för den här resursen.
    - **Resurs grupp**: skapa nya resurs grupper för nya resurser är en bra metod. Observera att detta kommer att användas i senare steg som **\<resource group name\>** .
    - **Tjänst information/namn**: ange **\<service instance name\>** .  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - **Plats**: Välj platsen för din tjänst instans.

    ![ASC-portalen börjar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicka på **Granska och skapa**.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

I följande procedur används Azure CLI-tillägget för att etablera en instans av Azure våren Cloud.

1. Logga in på Azure CLI och välj din aktiva prenumeration.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Förbered ett namn för din Azure våren Cloud-tjänst.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.

1. Skapa en resurs grupp som innehåller din Azure våren Cloud-tjänst.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Lär dig mer om [Azures resurs grupper](../azure-resource-manager/management/overview.md).

1. Öppna ett Azure CLI-fönster och kör följande kommandon för att etablera en instans av Azure våren Cloud.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Tjänst instansen tar cirka fem minuter att distribuera.
---
::: zone-end

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du Azure-resurser som kommer fortsätta att debiteras om de finns kvar i din prenumeration. Om du inte tänker fortsätta till nästa snabb start, se [Rensa resurser](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Annars går du vidare till nästa snabb start:

> [!div class="nextstepaction"]
> [Konfigurera konfigurationsserver](spring-cloud-quickstart-setup-config-server.md)
